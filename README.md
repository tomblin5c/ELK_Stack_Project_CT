## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

(Images/Cloud_Diagram)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  - activity1.yml = used to install DVWA servers
  - elk_playbook.yml = used to install ELK server
  - filebeat-playbook = used to install and configure Filebeat service on DVWA servers
  - metricbeat.yml = used to install and configure Metricbeat service on DVWA servers

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
	What aspect of security do load balancers protect?  What is the advantage of a jump box?_
	- Load balencers protect against Distributed Denial of Service (DDoS) attacks. They do this by receiving connections and distributing them between multiple servers accordingly. By doing so they provide a much higher amount of security and availablity while also inproving performance and even consumer satusfaction.
	- By utilizing a jumpbox you are using one secure device to gain access to the rest of the network. Connections to this one device can be extremely vetted for administrator use only but once a connection is made, an admin would have access to their network. This is far less invloved then setting up the security rules on each device the admin may or may not have to connect to while also allowing easier task completion such as updating.
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
What does Filebeat watch for?
	- Filebeat monitors changes to the files or their locations that are specified by the administrator. If any changes occurs, it collects the log files and indexes them based on a specified service like Elasticsearch.
What does Metricbeat record?
	- Metricbeat collects metrics from the docker servers and, like Filebeat, sends to a specified service to be indexed.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function   | IP Address | Operating System |
|----------|------------|------------|------------------|
| Jump Box | Gateway    | 10.0.0.4   | Linux            |
| Web-1    | Web Server | 10.0.0.5   | Linux            |
| Web-2    | Web Server | 10.0.0.6   | Linux            |
| ELK      | ELK Server | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
Add whitelisted IP addresses
	- 108.14.229.26

Machines within the network can only be accessed by Jump Box.
Which machine did you allow to access your ELK VM? What was its IP address?_
	- Personal Device, ip=108.14.229.26, via port 5601

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Available     | Allowed IP Addresses                            |
|------------|------------------------|-------------------------------------------------|
| Jump Box   | No                     | 108.14.229.26 (Personal IP) via port 22         |
| Web-1      | Yes, via Load Balancer | 40.117.43.126 (Red-Team-LB) 10.0.0.4 (Jump Box) |
| Web-2      | Yes, via Load Balancer | 40.117.43.126 (Red-Team-LB) 10.0.0.4 (Jump Box) |
| ELK Server | No                     | 108.14.229.26 (Personal IP) via port 5601       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
What is the main advantage of automating configuration with Ansible?
	- Using Ansible for automating configurations allows an administrator to spend more time in the detail and accuracy of the playbook rather than having to repeat the same configuration constantly for a great number of servers.

The playbook implements the following tasks:
In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
	- Name the playbook/Point to the group of machines to be targeted
		- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azureuser
  become: true
  tasks:
	- Install docker.io
		- name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
	- Install python3-pip
		- name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
	- Increase container's utilized memory
		- name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes
	- Download and launch ELK container/Sets Published Ports
		- name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
	-(Images/Elk_Container_Launch_Success_CT.jpg)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
List the IP addresses of the machines you are monitoring
	- Web-1: 10.0.0.5
	- Web-2: 10.0.0.6

We have installed the following Beats on these machines:
Specify which Beats you successfully installed
	- Filebeat
	- Metricbeat

These Beats allow us to collect the following information from each machine:
In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc.
	- Filebeat: Filebeat's data would consist of log files and their locations as specified by the administrator that completed the setup. An example of a log file that may be recorded by Filebeat is a system.auth which is prompted when establishing a connection and the system is authewnicating a user with said connection.
	- Metricbeat: Should show data based off of statistics regarding that device for instance, in looking through the metrics of a device one should be able to locate the CPU or memory being used by said device. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- Copy the playbook file to /etc/ansible/roles.
- Update the /etc/ansible/hosts file to include the specific ip address, and the group name, that you would like to run the mentioned playbook on. 
- Run the playbook, and navigate to http://[Puiblic_IP_of_ELK_Server]:5601/app/kibana to check that the installation worked as expected.

Answer the following questions to fill in the blanks:_
- Which file is the playbook? Where do you copy it?
	- elk_playbook.yml = used to install ELK server
	- filebeat-playbook = used to install and configure Filebeat service on DVWA servers
	- metricbeat.yml = used to install and configure Metricbeat service on DVWA servers
	- We write these playbook files to all be within the /etc/ansible/roles directory
- Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
	- /etc/ansible/hosts ; When editing the hosts file to add machines you must be sure to add them to the correct group so when you move on to creating your playbook for the ELK or Filebeat, you can specify which group you would like the playbook to target for the tasks listed below. 
- Which URL do you navigate to in order to check that the ELK server is running?
	- http://[Puiblic_IP_of_ELK_Server]:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc.
	-Command to ssh into any machine within network:
		**If entering the network from the public** ssh [Username]@[Public_IP_of_Machine]
		**If moving through the network from within** ssh [Username]@[Private_IP_of_Machine]
	-Command to add new machine IPs to /hosts file
		To View: cat /etc/ansible/hosts
		To Edit: nano /etc/ansible/hosts
	-Command to change directories to where configuration files are located:
		cd /etc/ansible/files
	-Command to change directories to where playbook files are located:
		cd /etc/ansible/roles
	-Command to edit/view configuration files:
		To View: cat [Configuration_Filename]
		To Edit: nano [Configuration_Filename]
	-Command to edit/view playbook files:
		To View: cat [Playbook_Name]
		To Edit: nano [Playbook_Name]
	- Command to run any specified playbook:
		sudo ansible-playbook [Playbook_Name]
