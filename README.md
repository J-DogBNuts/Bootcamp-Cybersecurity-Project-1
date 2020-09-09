# Bootcamp-Cybersecurity-Project-1
Project #1 (Week 13)
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

[Network Diagram](Images/Week13_NetworkDiagram_JBrasile.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Week13_NetworkDiagram_JBrasile.png file may be used to install only certain pieces of it, such as Filebeat.

  - filebeat-config.yml

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- Load Balancers protect availability by sending client requests to servers by efficiantly blanacing the load between online servers. Also allows for servers to be taken down for maintainence while leaving the others up and running, and allows for new servers to be added.
- Jump Boxes is advantages, as it allows you access and manage multiple machines on a virtual network and between security zones.  This allows the other VMs not to be exposed to the public internet.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the specific file and system uptime.
- Filebeat collect data about the file system.
- Metricbeat cllects metrics, like uptime, on a machine. i.e the health of the machine.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name                 | Function    | IP Address | Operating System     |
|----------------------|-------------|------------|----------------------|
| ElasticStack         | ELK Server  | 10.2.0.4   | Linux (Ubuntu 18.04) |
| Jump-Box-Provisioner | Gateway | 10.0.0.4   | Linux (Ubuntu 18.04) |
| Web-1                | Web Server  | 10.0.0.5   | Linux (Ubuntu 18.04) |
| Web-2                | Web Server  | 10.0.0.6   | Linux (Ubuntu 18.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Load Balancer machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 52.165.173.93

Machines within the network can only be accessed by the Jump Box.
- The ELK VM can be accessed by the Jump Box (10.0.0.4) and My Device Public IP

A summary of the access policies in place can be found in the table below.

| Name         | Publicly Accessible | Allowed IP Addresses                           |
|--------------|---------------------|------------------------------------------------|
| Jump Box     | No                  | 10.0.0.5 10.0.0.6 My Device Public IP          |
| ElasticStack | No                  | 10.0.0.4 10.0.0.5 10.0.0.6 My Device Public IP |
| RedTeamLB    | Yes                 | 10.0.0.5 10.0.0.6                              |
| Web-1        | No                  | 10.0.0.4 52.165.173.93                         |
| web-2        | No                  | 10.0.0.4 52.165.173.93                         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- You can set the perameters and it allows you to duplicate once you have created a playbook.

The playbook implements the following tasks:
- Download and install Docker.io and Python3-pip;
- Installs pip package Docker;
- Increase virtual memory to 262144 and use 'sysctl' to make it presistent, even after VM restart;
- Download and launch elk container and list what ports to run on.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[](Images/ElkStack_Day1_ScreenShot01_docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.5 and 10.0.0.6

We have installed the following Beats on these machines:
- Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log data / files from a server.  Metricbeat collects metrics and statistics OS and services runing a machine.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook install-elk.yml file to root@8fdb4bbf39d6:/etc/ansible.
- Update the host.yml file to include [elk] 10.2.0.4 ansible_python_interpreter=/usr/bin/python3
- Run the playbook, and navigate to http://168.61.68.246:5601/app/kibana to check that the installation worked as expected.

Commads to download ELK yml file, update and intall:
- touch /etc/ansible/install-elk.yml
- nano install-elk.yml
- Configuration of 'install-elk.yml' file:
  ``` bash
     - ---
     - name: Configure Elk VM with Docker
       hosts: elk
       become: true
       tasks:
         # Use apt module
         - name: Install docker.io
           apt:
             update_cache: yes
             name: docker.io
             state: present

           # Use apt module
         - name: Install python3-pip
           apt:
             name: python3-pip
             state: present

           # Use pip module (It will default to pip3)
         - name: Install Docker module
           pip:
             name: docker
             state: present

           # Use command module
         - name: Increase virtual memory
           command: sysctl -w vm.max_map_count=262144

           # Use sysctl module
         - name: Use more memory
           sysctl:
             name: vm.max_map_count
             value: '262144'
             state: present
             reload: yes

           # Use docker_container module
         - name: download and launch a docker elk container
           docker_container:
             name: elk
             image: sebp/elk:761
             state: started
             restart_policy: always
             # Please list the ports that ELK runs on
             published_ports:
              - 5601:5601
              - 9200:9200
              - 5044:5044
'"
 - ansible-playbook /etc/ansible/install-elk.yml
         
