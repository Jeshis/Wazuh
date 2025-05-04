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
![image](https://github.com/user-attachments/assets/28c2a74c-10c4-4702-85d1-4bf1d8cb7358)  
![image](https://github.com/user-attachments/assets/7bab303e-30e0-4f93-a190-a44d1f0595a2)

## Adding wazuh agent in Windows   
Using Powershell (adminstrator privilege)  
* Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.11.2-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='Server_IP' WAZUH_AGENT_NAME='Windows'   
*  NET START WazuhSvc    
Agents files are stored in C:\Program Files (x86)\ossec-agent   

using GUI
- You just need to run windows-agent.msi and fill manager ip & authentication key if required.  
- You can find the package in below offical wazuh link.  
- https://packages.wazuh.com/4.x/windows/wazuh-agent-4.11.2-1.msi  

You can check whether agent is active or not from Windows Task Manager.  
![image](https://github.com/user-attachments/assets/6963b08e-8b61-4cc0-a9e7-4659f42e0b78)  

## Adding wazuh agent in MacOS
### For intel based
From CLI
- curl -so wazuh-agent.pkg https://packages.wazuh.com/4.x/macos/wazuh-agent-4.11.2-1.intel64.pkg && echo "WAZUH_MANAGER='Server_IP' && WAZUH_AGENT_NAME='mac_intel'" > /tmp/wazuh_envs && sudo installer -pkg ./wazuh-agent.pkg -target /
- sudo /Library/Ossec/bin/wazuh-control start  

All files are stored in /Library/Ossec/  

From GUI  
You can download from this link for intel based architecture  
https://packages.wazuh.com/4.x/macos/wazuh-agent-4.11.2-1.intel64.pkg  

### For Apple silicon based  
From CLI  
- curl -so wazuh-agent.pkg https://packages.wazuh.com/4.x/macos/wazuh-agent-4.11.2-1.arm64.pkg && echo "WAZUH_MANAGER='172.16.210.218' && WAZUH_AGENT_NAME='mac-silicon'" > /tmp/wazuh_envs && sudo installer -pkg ./wazuh-agent.pkg -target /
- sudo /Library/Ossec/bin/wazuh-control start

From GUI  
You can download from this link for apple silicon based architecture   
https://packages.wazuh.com/4.x/macos/wazuh-agent-4.11.2-1.arm64.pkg  


 


