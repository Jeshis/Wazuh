# Installing Wazuh in Ubuntu server.
## Installation workflow:  
* wazuh-indexer : Scalable full-text search and analytics engine that indexes and stores alerts from the Wazuh server.  
* wazuh-manager : Processes agent data using decoders, rules, and threat intelligence to detect IOCs; supports remote agent management and scales horizontally in clustered setups.  
* wazuh-dashboard : web user interface for data visualization and analysis  
After wazuh installation, agents are peered through wazuh-agents - that sends logs from endpoints.  

# Wazuh-indexer installation:  
## Certificate Creation
1. Generating SSL certificates  
 - curl -sO https://packages.wazuh.com/4.11/wazuh-certs-tool.sh  
 - curl -sO https://packages.wazuh.com/4.11/config.yml  
2. Edit ./config.yml and replace the node names and IP values
   
![image](https://github.com/user-attachments/assets/5ebf72f0-a1d1-4058-acbb-1629c4089c7c)  

4. Run ./wazuh-certs-tool.sh to create the certificates.  
 - bash ./wazuh-certs-tool.sh -A
![image](https://github.com/user-attachments/assets/ccc9121a-609b-4998-91ab-1a9a55c98c49)
6. Compress all the necessary files.  
 - tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .  
 - rm -rf ./wazuh-certificates  

## Nodes Installation  
+ Installing package dependencies   
 - apt-get install debconf adduser procps
+ Adding the Wazuh repository  
 - apt-get install gnupg apt-transport-https
1. Install the GPG key.  
 - curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
2. Add the repository.  
 - echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
3. Update packages  
 - apt-get update  

## Installing the Wazuh indexer  
* Install Wazuh indexer package.  
 - apt-get -y install wazuh-indexer  
* Configuring Wazuh indexer   
1. Edit /etc/wazuh-indexer/opensearch.yml file and replace:
   - network.host : Use the same node address set in config.yml to create the SSL certificates.
   - node.name: node as defined in the config.yml file.
   - cluster.initial_master_nodes :
   - discovery.seed_hosts:
   - plugins.security.nodes_dn:
   - ![image](https://github.com/user-attachments/assets/d34a5f91-5d4f-4090-b51f-fda665054160)

* Deploying certificates
  - Check wazuh-certificates.tar file  
1. Run commands replacing <INDEXER_NODE_NAME> with the name of the Wazuh indexer node you are configuring as defined in config.yml.  
   - NODE_NAME=<INDEXER_NODE_NAME>  
   - mkdir /etc/wazuh-indexer/certs  
   - tar -xf ./wazuh-certificates.tar -C /etc/wazuh-indexer/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem  
   - mv -n /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/wazuh-indexer/certs/indexer.pem  
   - mv -n /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/wazuh-indexer/certs/indexer-key.pem  
   - chmod 500 /etc/wazuh-indexer/certs  
   - chmod 400 /etc/wazuh-indexer/certs/*  
   - chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs

   
