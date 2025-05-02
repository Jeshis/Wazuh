# Wazuh Agent  
![image](https://github.com/user-attachments/assets/737d2f6b-793d-4d24-927e-d34a52bb7a62)  

* Wazuh agent is a cross-platform component that runs on the monitored endpoints. It securely transmits data to the Wazuh server in near real-time using an encrypted and authenticated connection.  
* Agent can be added from GUI or CLI.  

## Adding wazuh agent in linux endpoints:  
* From CLI  
- Add a repo  
  - curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg  
  - echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list  
  - apt-get update  
- Deploy agent  
  - WAZUH_MANAGER="10.0.0.2" apt-get install wazuh-agent  (server IP)
  - systemctl daemon-reload  
  - systemctl enable wazuh-agent
  - systemctl start wazuh-agent (this will start agent everytime systems runs)

* From GUI  
  Home>Endpoints>Deploy a new agent  
  select machine, Fill server IP, Name Agent  
![image](https://github.com/user-attachments/assets/8566094f-806e-45b0-8367-3e3a3adbb960)  
![image](https://github.com/user-attachments/assets/8ec2e44a-4a28-407d-af64-27f5dd1ed94d)  
![image](https://github.com/user-attachments/assets/7bab303e-30e0-4f93-a190-a44d1f0595a2)


 


