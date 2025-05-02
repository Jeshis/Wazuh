# Wazuh
open source security application that unifies Extended Detection & Response and SIEM capablities. 

# Installing Wazuh in Ubuntu server.
Installation workflow:  
1- wazuh-indexer : Scalable full-text search and analytics engine that indexes and stores alerts from the Wazuh server.  
2- wazuh-manager : Processes agent data using decoders, rules, and threat intelligence to detect IOCs; supports remote agent management and scales horizontally in clustered setups.  
3- wazuh-dashboard : web user interface for data visualization and analysis  
After wazuh installation, agents are peered through wazuh-agents - that sends logs from endpoints.  

#Wazuh-indexer installation:
*Certificate Creation
1. Generating SSL certificates  
curl -sO https://packages.wazuh.com/4.11/wazuh-certs-tool.sh  
curl -sO https://packages.wazuh.com/4.11/config.yml  
2. Edit ./config.yml and replace the node names and IP values
   
![image](https://github.com/user-attachments/assets/5ebf72f0-a1d1-4058-acbb-1629c4089c7c)  

4. Run ./wazuh-certs-tool.sh to create the certificates.  
bash ./wazuh-certs-tool.sh -A
![image](https://github.com/user-attachments/assets/ccc9121a-609b-4998-91ab-1a9a55c98c49)
6. Compress all the necessary files.  
tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .  
rm -rf ./wazuh-certificates  

*Nodes Installation




