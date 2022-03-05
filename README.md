## Red v. Blue Project
Description: As a group we performed a security assessment against a target from the both offensive and defensive perspectives. Individually, we're presenting the findings to a hypothetical client. 

## Network Topology: 
    - ![Network Diagram]()
    
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing. 
- Metricbeat records the metrics and statistics that it has collected and sends them to the output that is specified, such as Elasticsearch or Logstash.
    
| Name          | Function       | IP Address                 | Operating System |
|---------------|----------------|----------------------------|------------------|
| Azure Network VM    | Gateway    | 10.0.0.43 / 192.168.1.1  |                  |
| Elk Server          | Log Server | 192.168.1.100            | Ubuntu Linux     |
| Capstone/Web server | Target     | 192.168.1.105            | Ubuntu Linux     |
| Kali computer       |  Attacker  | 192.168.1.90             | Kali Linux       |

## Red Team: Security Assessment
    Vulnerability Assesssment: 
        - Brute Force Vulnerabilities: 
            Description: Weak password policy. Inability to lockout accounts when failed logins occur during brute force attacks.  
            Impact: Brute force attacks succeed in allowing access to confidential information that led to compromising the webserver.
        - Sensitive Information Disclosure:
            Description: Allows users browser to read full contents of directories on web server.
            Impact: Allowed attackers to determine location and means to access: /company_folders/secret_folder/
        - File Upload Vulnerabilities: 
            Description: Allows access into confidential files on a site and ability to copy malicious scripts onto a target server.
            Impact: Allows attackers to gain access, and run malicious scripts to allow establish reverse shell.
            
    Exploitation: 
        - Brute Force Vulnerabilities: 
            Tools and Processes: Used Hydra to execute a brute force dictionary attack through bash to get the password for Ashton’s account. 
            Achievements: The password for Ashton was successfully found using Hydra. This gave access to the company_folders/secret_folder,
                          which had a hash of Ryan’s password. Reverse engineering the hash gave the password for Ryan’s account and access 
                          to Webdav on 192.168.1.105. 
            photos
        - Sensitive Information Disclosure:
            Tools and Processes:A web browser accessed the apache web server and was able to access the webpage: 192.168.1.105 over HTTP.   
                                There, company_folder was accessible. This allows a potential attacker to drill down to unauthorized folders,
                                such as company_folder/ secret_folder 
            Achievements: Using Ashton’s account info, I was able to access unauthorized material when I drilled down to  
                          192.168.1.105/company_folders/secret_folder. This enabled me to get info on Ryan’s account and then instigate the 
                          reverse shell from 192.168.1.105

             - ![Network Diagram]()
        - File Upload Vulnerabilities: 
            Tools and Processes: Having the password to Ryan’s account, a reverse shell php malicious payload was placed on the Web Server 
                                 in the webdav directory. Then the file was accessed via browser and it was then executed. 
            Achievements: The attack machine executed the default handler with msfconsole and once the php scripted was executed it opened up 
                          the port to establish the connection from the target machine to the attack machine, establishing the reverse shell.
            photos
    

## Blue Team: Log Analysis and Attack Characterization
    Analysis: 
        - Identifying the port Scan 
        - Finding the Request for the Hidden Directory
        - Uncovering the Brute Force Attack
        - Finding the WebDav Connection
## Hardening: Proposed Alarms and Mitigation Strategies
        - Blocking the Port Scan: 
            - Alarm:
            - System Hardening: 
        - Finding the request for the Hidden Directory:
            - Alarm:
            - System Hardening: 
        - Preventing Brute Force Attacks: 
            - Alarm:
            - System Hardening: 
        - Detecting the WebDav Connection: 
            - Alarm:
            - System Hardening: 
        - Identifying Reverse Shell Uploads: 
            - Alarm:
            - System Hardening:





