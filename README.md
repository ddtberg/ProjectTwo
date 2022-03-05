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
        - Sensitive Information Disclosure:
        - File Upload Vulnerabilities: 
    

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





