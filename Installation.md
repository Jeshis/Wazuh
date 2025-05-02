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
  + apt-get -y install wazuh-indexer  
* Configuring Wazuh indexer   
1. Edit /etc/wazuh-indexer/opensearch.yml file and replace:
   - network.host : Use the same node address set in config.yml to create the SSL certificates.
   - node.name: node as defined in the config.yml file.
   - cluster.initial_master_nodes :
   - discovery.seed_hosts:
   - plugins.security.nodes_dn:  
   - ![image](https://github.com/user-attachments/assets/6c63b375-2941-43df-a830-8fdfafdc8f56)  

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
### NOTE:
 If no other Wazuh components are going to be installed on this node, remove the wazuh-certificates.tar file by running rm -f ./wazuh-certificates.tar to increase security.
##  Starting the service
* systemctl daemon-reload  
* systemctl enable wazuh-indexer  
* systemctl start wazuh-indexer

### NOTE:
Disable Wazuh Updates
  - sed -i "s/^deb /#deb /" /etc/apt/sources.list.d/wazuh.list  
  - apt update  

## Cluster Initialization  
  - /usr/share/wazuh-indexer/bin/indexer-security-init.sh
to load to load the new certificates information and start the single-node or multi-node cluster.
### Testing the cluster installation  
![image](https://github.com/user-attachments/assets/6bd51cf3-23b9-47b6-8e61-03f6fee9009e)  

# Installing Wazuh server  
Two stages: 1. Wazuh server node installation  2. Cluster configuration for multi-node deployment  
## 1. Wazuh Server node installation  
If you have already configured and enabled Wazuh repository, then you do not need to add again. You can directly install  server node.  
- apt-get -y install wazuh-manager  

* Installing Filebeat package (ightweight data shipper designed to securely forward alerts and archived events processed)
  - curl -O https://packages.wazuh.com/4.x/apt/pool/main/f/filebeat/filebeat-oss-7.10.2-amd64.deb  
  - apt-get install ./filebeat-oss-7.10.2-amd64.deb  

1. Configuring Filebeat
  - curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.11/tpl/wazuh/filebeat/filebeat.yml  
2. Edit the /etc/filebeat/filebeat.yml configuration file with localhost.  
![image](https://github.com/user-attachments/assets/51585b4b-a77e-4d7b-8625-50343b56ee04)
3. Create a Filebeat keystore to securely store authentication credentials.
  - filebeat keystore create  
4. Add the default username and password to the secrets keystore.(admin:admin)  
  - echo admin | filebeat keystore add username --stdin --force  
  - echo admin | filebeat keystore add password --stdin --force  
5. Download the alerts template for the Wazuh indexer.
  - curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/v4.11.2/extensions/elasticsearch/7.x/wazuh-template.json  
  - chmod go+r /etc/filebeat/wazuh-template.json  
6. Install the Wazuh module for Filebeat.
  - curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.4.tar.gz | tar -xvz -C /usr/share/filebeat/module  

## Deploying Certificates 
Note: Check whether ![image](https://github.com/user-attachments/assets/22c8f2f0-50e5-480b-b3ac-ff1f97a472a2) is available or not in the system.  
1. Replace <SERVER_NODE_NAME> with Wazuh server node certificate name, i.e used in config.yml when creating the certificates  
  - NODE_NAME=<SERVER_NODE_NAME>  
Now, move files in relevant location.
  -  mkdir /etc/filebeat/certs  
  - tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem  
  - mv -n /etc/filebeat/certs/$NODE_NAME.pem /etc/filebeat/certs/filebeat.pem  
  - mv -n /etc/filebeat/certs/$NODE_NAME-key.pem /etc/filebeat/certs/filebeat-key.pem  
  - chmod 500 /etc/filebeat/certs  
  - chmod 400 /etc/filebeat/certs/*  
  - chown -R root:root /etc/filebeat/certs  




   
