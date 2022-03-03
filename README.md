## Red v. Blue Project
The files in this repository were used to configure the network depicted below.

- ![Network Diagram](https://github.com/ddtberg/ProjectOne/blob/main/Diagrams/Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ansible playbook files may be used to install only certain pieces of it, such as Filebeat.

  - [Ansible.cfg](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/Ansible.cfg)
  - [hosts](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/hosts)
  - [install-elk.yml](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/install-elk.yml)
  - [filebeat-config.yml](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/filebeat-config.yml)
  - [filebeat-playbook.yml](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/filebeat-playbook.yml)
  - [metricbeat-config.yml](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/metricbeat-config.yml)
  - [metricbeat-playbook.yml](https://github.com/ddtberg/ProjectOne/blob/main/Ansible/metricbeat-playbook.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network. An aspect of security that a load balancer protects is against Denial of Service attacks. The advantage of a jump box is that it isolates high value assets as it limits the means of possible attack. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing. 
- Metricbeat records the metrics and statistics that it has collected and sends them to the output that is specified, such as Elasticsearch or Logstash.

The configuration details of each machine may be found below.

| Name          | Function       | IP Address                 | Operating System |
|---------------|----------------|----------------------------|------------------|
| Jump box      | Gateway        | 10.0.0.4                   | Linux            |
| Web1          | Web Server     | 10.0.0.5                   | Linux            |
| Web2          | Web Server     | 10.0.0.6                   | Linux            |
| Elk           | Elk Server     | 10.1.0.4\52.188.227.87     | Linux            |
| Load Balancer | Load Balancer  | 20.124.128.228             | linux            |
| Work Station  | Access Control | Public IP                  | linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the ELK server can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- a Workstation's Public IP through TCP Port 5601

Machines within the network can only be accessed by Workstation and the Jump Box Provisioner.
- Jump Box Provisioner, IP : 10.0.0.4 via SSH port 22
- a Workstation w/ a Public IP via port TCP 5601

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP addresses                       |
|---------------|---------------------|--------------------------------------------|
| Jump box      | No                  | Workstation Public IP using SSH via TCP 22 |
| Web1          | No                  | 10.0.0.4 on SSH 22                         |
| Web2          | No                  | 10.0.0.4 on SSH 22                         |
| Elk           | No                  | Workstation Public IP using TCP 5601       |
| Load Balancer | No                  | Workstation Public IP using TCP 80         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible allows for quick deployment and configuration of multiple apps across multiple VMs. This is possible through the usage of a playbook, which allows you to target the specific machines to install specific software with a specific configuration. 

The Playbook implements the following tasks: 
- Installs Docker.io, configures said Docker.io, and establishes the published ports for the container. 
- Installs and configures pip3.
- Increases virtual memory and ensures it has sustains that level of ram on restart. 

### Target Machines & Beats

This ELK server is configured to monitor the following machines:
- Web1 : 10.0.0.5
- Web2 : 10.0.0.6

We have installed the following Beats on these machines:
-  ELK Server, Web1 and Web2
-  The ELK Stack Installed are: FileBeat and MetricBeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log events.
- Metricbeat collects metrics from your system and services.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- **In general:** 
    - Update the Hosts file in the Ansible directory to include [webservers] and [ELK]. Also include the local IP addresses under the heading. 
    - Update the ansible.cfg to include your remote user name that you will use to access said servers. 

- **For ELK VM Installation and Configuration:**
    - Copy the Ansible ELK installationation and configuration file to the /etc/ansible/ folder. 
    - 
    - Run the playbook with the following command: ansible-playbook install-elk.yml
    - As stated previously, this will install the docker and configure it, and other necessary configurations.

- **For Filebeat:**
     - Copy the Ansible Filebeat playbook and configuration file to /etc/ansible/ folder.
     - Edit the Filebeat configuration file to point to the VM that hosts the ELK server. 
     - Edit the Filebeat Playbook and confirm it has the following: 
         - Hosts: webservers 
           - This will ensure that Filebeat is installed on the webservers listed in the Host file
         - curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
           - This will ensure it is downloading the correct version when it installs Filebeat on the system. 
         - Confirm that the playbook copies filebeat-config.yml and places it in /etc/filebeat/ and renames the file to filebeat.yml
           - This copies the Filebeat config file we configured and copies it to the webservers so it can correctly point to the Elk Server. 
     - Run the Ansible Filebeat Playbook: filebeat-playbook.yml
         - This will download and install Filebeat, set it up, and ensure it starts on reboot. 

- **For MetricBeat:**
     - Copy the Ansible Metricbeat playbook and configuration file to /etc/ansible/ folder.
     - Edit the Metricbeat configuration file to point to the VM that hosts the ELK server.
     - Edit the Metricbeat Playbook and confirm it has the following: 
         - Hosts: webservers 
           - This will ensure that metricbeat is installed on the webservers listed in the Host file
         - curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb
           - This will ensure it is downloading the correct version when it installs metricbeat on the system. 
         - Confirm that the playbook copies metricbeat-config.yml and places it in /etc/metricbeat/ and renames the file to metricbeat.yml
           - This copies the MetricBeats config file we configured and copies it to the webservers so it can correctly point to the Elk Server. 
     - Run the Ansible Metricbeat Playbook: metricbeat-playbook.yml
         - This will download and install Metricbeat, set it up, and ensure it starts on reboot.  
 
 - **To confirm the success of the installations**:
     - Go to the Kibana URL, this will be the Public IP address of the ELK server appended with :5601/app/kibana appended at the end.
       - Example: http://20.63.76.153:5601/app/kibana
     - **For Filebeat:** On the Kibana webpage go to Logs > Add log data > System logs > Module Status to confirm that the installation was successful.
     - **For Metricbeat:** On the Kibana webpage go to Metrics > Add Metric Data > Docker Metrics > Module Status to confirm that the installation was successful.
