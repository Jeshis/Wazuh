# Adding Mikrotik Router in Wazuh   
There are two ways of adding Mikrotik router in Wazuh.  
1. You can setup a ubuntu server and setup a Rsyslog server in ubuntu to forward the Mirkotik logs to Wazuh server. In this method, You can monitor the Mikrotik as an agent. Easy to identity.  
![image](https://github.com/user-attachments/assets/e1f58529-f195-4843-a22c-3662ebfc8683)
2. Another way is you can directly forward the Mikrotik logs to Wazuh through wazuh syslog sever. You cannot see as a seperate agent as wazuh server behaves like its own log and send alerts in the dashboard with detailed info


   


