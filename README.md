# Wazuh
open source security application that unifies Extended Detection & Response and SIEM capablities. 

# Installing Wazuh in Ubuntu server.
Installation workflow:  
1- wazuh-indexer : Scalable full-text search and analytics engine that indexes and stores alerts from the Wazuh server.  
2- wazuh-manager : Processes agent data using decoders, rules, and threat intelligence to detect IOCs; supports remote agent management and scales horizontally in clustered setups.  
3- wazuh-dashboard : web user interface for data visualization and analysis  
After wazuh installation, agents are peered through wazuh-agents - that sends logs from endpoints.  
