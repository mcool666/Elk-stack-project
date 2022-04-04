## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Path to image file. Google drive https://drive.google.com/file/d/1ELv1BwAieE-asJxfFkHI2Jc2S2z2gBn6/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. 
Alternatively, select portions of the yml and config file may be used to install only certain pieces of it, such as Filebeat.

ansible_config.yml Playbook

---
- name: Config Web VM with Docker                           
  hosts: webservers
  become: true
  tasks:
    - name: docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: Install pip3
      apt:
        name: python3-pip
        state: present

    - name: Install Docker python module
      pip:
        name: docker
        state: present

    - name: download and launch a docker web container
      docker_container:
        name: dvwa
        image: cyberxsecurity/dvwa
        state: started
        restart_policy: always
        published_ports: 80:80

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes



  This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
- Load Balancers protect Availability, Web Traffic, Web Security
- The advantages of jump box are automation, security, network Segmentation, and access Control

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VM’s on the network and system metrics.
- Filebeat monitors log files or locations you specify.
- Metricbeat collects stats and metrics and moves them to an output such as Elasticsearch. Metricbeat helps you monitor your servers by collecting these stats and metrics

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| DVWA 1   |Web Server| 10.0.0.5   | Linux            |
| DVWA 2   |Web Server| 10.0.0.6   | Linux            |
| ELK      |Elk server| 10.1.0.4   | Linux            |
|Load Balancer|Load Balancer|Static IP|Linux          |   

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the ELK server machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Public IP tcp 5601 is allowed access.

Machines within the network can only be accessed by each other.
- Jump-Box-Provisioner IP : 10.0.0.4 via SSH port 22
- Workstation Public IP via port TCP 5601

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes/No              | 10.0.0.1 10.0.0.2    |
|          |                     |                      |
|          |                     |                      |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- One of the main advantages of automating config with ansible is it is relatively easy to do and you can edit it easily.

The playbook implements the following tasks:
1) We will need to add our ELK vm to our Ansible host file. We will create an Anisble playbook for the ELK VM.

Install_Elk.yml

Specify group of machines and remote user
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  tasks:
    
    

2) Before we run the container we will need to increase memory

- name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
3) We will install docker.io, Python3-pip, and docker python pip module

- name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
- name: Install Docker python module
      pip:
        name: docker
        state: present
- name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

- launching and Exposing the container with these published ports:
 `5601:5601` 
 `9200:9200`
 `5044:5044`


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

sysadmin@elk:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                                              NAMES
72c189987fad        sebp/elk            "/usr/local/bin/star…"   3 hours ago         Up 3 hours          0.0.0.0:5044->5044/tcp, 0.0.0.0:5601->5601/tcp, 0.0.0.0:9200->9200/tcp, 9300/tcp   elk


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.5
- 10.0.0.6 

We have installed the following Beats on these machines:
- ELK, Web1, and Web2 have Filebeats and Metricbeats

These Beats allow us to collect the following information from each machine:
- Filebeat helps generate and organize log files to send to Logstash and Elasticsearch. Specifically, it logs information about the file system, including which files have changed and when.
- Metricbeat is a lightweight shipper that you can install on your servers to periodically collect metrics from the operating system and from services running on the server.
Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the /etc/ansible/files/filebeat-config.yml' file to '/etc/filebeat/filebeat-playbook.yml.
- Update the filebeat-config.yml file root@72c189987fad:/etc/ansible/files# nano filebeat-config.yml
- Run the playbook, and navigate to Kibana > Logs : Add log data > System logs > 5:Module Status > Check data.


- The playbook is filebeat-playbook.yml and you copy it into your container(root@72c189987fad:ansible# ansible-playbook filebeat-playbook.yml) 
- You update the filebeat config file to choose what machines to install on. How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- http://[your.VM.IP]:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
