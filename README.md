## Red v. Blue Project

**Project Description**: As a group we performed a security assessment against a target from the both offensive and defensive perspectives. Individually, we're presenting the findings to a hypothetical client. 

### Network Topology:
![Network Diagram](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/diagram.png)
    
| Name          | Function       | IP Address                 | Operating System |
|---------------|----------------|----------------------------|------------------|
| Azure Network VM    | Gateway    | 10.0.0.43 / 192.168.1.1  |         N/A      |
| Elk Server          | Log Server | 192.168.1.100            | Ubuntu Linux     |
| Capstone/Web server | Target     | 192.168.1.105            | Ubuntu Linux     |
| Kali computer       |  Attacker  | 192.168.1.90             | Kali Linux       |

 ### Red Team: Security Assessment
 
#### Vulnerability Assesssment: 
- **Brute Force Vulnerabilities**: 
	- **Description**: Weak password policy. Inability to lockout accounts when failed logins occur during brute force attacks.
	
	- **Impact**: Brute force attacks succeed in allowing access to confidential information that led to compromising the webserver.
	
- **Sensitive Information Disclosure**:
	- **Description**: Allows users browser to read full contents of directories on web server. 
	
	- **Impact**: Allowed attackers to determine location and means to access: /company_folders/secret_folder/
	    
 - **File Upload Vulnerabilities**: 
 	- **Description**: Allows access into confidential files on a site and ability to copy malicious scripts onto a target server.
 	
	- **Impact**: Allows attackers to gain access, and run malicious scripts to allow establish reverse shell.

#### Exploitation: 
- **Brute Force Vulnerabilities**: 
	- **Tools and Processes**: Used Hydra to execute a brute force dictionary attack through bash to get the password for Ashton’s account. 
	- **Achievements**: The password for Ashton was successfully found using Hydra. This gave access to the company_folders/secret_folder, which had a hash of Ryan’s password. Reverse engineering the hash gave the password for Ryan’s account and access to Webdav on 192.168.1.105.
 
- **Sensitive Information Disclosure**:
	- **Tools and Processes**: A web browser accessed the apache web server and was able to access the webpage: 192.168.1.105 over HTTP. There, company_folder was accessible. This allows a potential attacker to drill down to unauthorized folders, such as company_folder/ secret_folder.
	- **Achievements**: Using Ashton’s account info, I was able to access unauthorized material when I drilled down to 192.168.1.105/company_folders/secret_folder. This enabled me to get info on Ryan’s account and then instigate the reverse shell from 192.168.1.105

- **File Upload Vulnerabilities**: 
	- **Tools and Processes**: Having the password to Ryan’s account, a reverse shell php malicious payload was placed on the Web Server in the webdav directory. Then the file was accessed via browser and it was then executed. 
	- **Achievements**: The attack machine executed the default handler with msfconsole and once the php scripted was executed it opened up the port to establish the connection from the target machine to the attack machine, establishing the reverse shell.
    

### Blue Team: Log Analysis and Attack Characterization

-  **Identifying the port Scan**: 
	- **Time port scan occurred**: February 7th at 4:30 
	- **Number of packets sent**: 25,046
	- **IP address that sent sent packets**: 192.168.1.90
	- **Indicator of a port scan**: The sudden influx of traffic indicates at 4:30 would indicate a port scan.
	![Port Scan](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/Identifying%20the%20Port%20Scan.png)

-  **Finding the Request for the Hidden Directory**:
	- **Time the request occurred**: February 7th between 4:40 and 4:45
	- **Number of requests made**: 15,472
	- **Files requested**: “connect_to_corp_server” 
	- **Files contained**:  Instructions how to connect to the WebDav on the server.
	![Hiddent Directory](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/Finding%20the%20request%20for%20the%20Hidden%20Directory.png)
	    
- **Uncovering the Brute Force Attack**: 
	
	- **Number of requests made during the attack**: 15, 472
	![Brute Force](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/Brute%20Force%20Two.png)
	- **Time the password was successfully hacked**: Feb 7, 4:42:36
	![Brute Force](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/Uncovering%20ther%20Brute%20Force%20Attack.png)
	    
- **Finding the WebDav Connection**: 
	
	- **Total Number of requests made to the Webdav directory**: 230
		- **WebDav/** - 96 requests
		- **WebDav/shell.php** - 134 requests
	![WebDav One](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/WebDav%20Connection%20One.png)
	- **Time Shell.php was placed in WebDav**: Feb 7, at 4:53:07
	![WebDavTwo](https://github.com/ddtberg/ProjectTwo/blob/main/Pictures/WebDav%20Connection%20Two.png)
	

### Hardening: Proposed Alarms and Mitigation Strategies
- **Blocking the Port Scan**: 
	- **Alarm**:
		- **Report Purpose**: To detect future port scans.
		- **Report detection standard**: Number of ports accessed per second from the same IP. 
			- **Excluding**: 
				- **Source.IP / Destination.IP**: 192.168.1.105,
				- **Source.port / Destination.port**: 22, 80
		- **Alarm Threshold**: Alert sent via email, if 5 or more detections occur per second, per same IP address. 
	- **System Hardening**: 
		- **Firewall block all incoming and outgoing scans except for necessary ports**: 
			- Currently only ports 80 and 22 are open. Port 80 can be left open since it’s a web server, but port 22 could be potentially closed.  
		- **Ways to prevent specific port scanning techniques**: 
			- Disabling ICMP on the firewall or router for external traffic, while enabling for internal uses could prevent general ping scans. However, an IDS would likely be needed for TCP Connect or TCP Half Open port scans. Additionally, only more recent IDS versions are able to detect some stealth scanning techniques.

- **Finding the request for the Hidden Directory**:
	- **Alarm**:
		- **Report Purpose**: To detect unauthorized access to the hidden directory.
		- **Report detection standard**: Number of times the hidden directory has been accessed. Focusing on the Source.ip. 
			- **Excluding**:
				- **Source.ip**: 192.168.1.105
		-  **Alarm Threshold**: Alert sent via email, if any attempts to access the folder. Some will likely be authorized, but it would be good to know if any access did occur. 
	- **System Hardening**: 
		- Disable directory listing in apache to remove indexes for directory information.
		- Modify the httpd.conf file on the web server to block access to unauthorized files if their IP is not listed. 
                     	>nano /etc/httpd/conf/httpd.conf
                        	 <Directory/var/www/company_folders/secret_folder/>
                          	  Order allow, deny
                               	 	Allow from 192.168.1.105
                               	 	Deny from 192.168.1.90
                        	 </Directory>
		 - This allows you to enable access for the web server, but preclude access from the attacker. This can be expanded to other desired ranges for both allowance or denial. 

- **Preventing Brute Force Attacks**: 
	- **Alarm**:
		- **Report Purpose**: To detect future brute force attacks. 
		- **Report detection** standard: To detect Http.response.status_code: 401 related to the url.path:”/company_folders/secret_folder/" using http.request:”get” and user_agent.original :"Mozilla/4.0 (Hydra)"
		- **Alarm Threshold**: Alert email and log when the number of those specific errors reach more than 1,000 within a minute. 
	- **System Hardening**:
		- **A Stronger password policy would greatly help here**:
			- Limit login attempts 
			- Two factor authentication
                    	- CAPTCHA
                   	- Increase password length and complexity to standard business practices. 
                   	- Limit login attempts to those only from specific ip ranges. 
                    	- Create unique login URLs for different user groups 
- **Detecting the WebDav Connection**: 
	- **Alarm**:
		- **Report Purpose**: To detect unauthorized access of the webdav directory.
		- **Report detection standard**: The number of the times the webdav directory has been accessed using: http.request.method:* and url.path: *webdav* 
		- **Alarm Threshold**: Alert email and log when any access has occurred in the webdav directory.
	- **System Hardening**: 
		- As before, you can modify the httpd.conf file on the web server to block access to the Webdav directory if their IP is not listed or is labeled under deny.
                       >nano /etc/httpd/conf/httpd.conf
                         <Directory/var/www/company_folders/secret_folder/>
                            Order allow, deny
                                Allow from 192.168.1.105
                                Deny from 192.168.1.90
                         </Directory>
		- Additionally, webdav should be turned off entirely. Network drives can be used to accomplish the same task and be more secure. 

            
            
- **Identifying Reverse Shell Uploads**: 
	- **Alarm**:
		- **Report Purpose**: To detect unauthorized file uploads. 
 		- **Report detection standard**: The number of file uploads to the webdav directory utilizing: http.method: “put”, and url.path: *webdav* 
 		- **Alarm Threshold**: Alert email and log when access when any file is placed in the webdav directory. 
	- **System Hardening**:
		- **Preventing or limiting the ability to execute files uploaded to the server**:
			- Scramble uploaded file names and extensions
			- Specifically define valid types of files that the user is allowed to upload.  
			- Require authentication to upload files







