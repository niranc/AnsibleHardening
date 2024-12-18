# AnsibleHardening
Hardening Linux Server with Ansible ...

Ansible is an open source IT automation engine that automates provisioning, configuration management, application deployment, orchestration, and many other IT processes. It is free to use, and the project benefits from the experience and intelligence of its thousands of contributors.
What We Did
In this article, we covered essential practices for hardening both the operating system and SSH configurations to enhance security.
Ansible Code Structuring Organized our Ansible playbooks and roles for streamlined configuration management. Established a clear structure to facilitate scalability and reusability of automation code.
Operating System Hardening Key steps for securing the OS, including system updates, disabling unnecessary services, and securing system configurations.
SSH Hardening Configurations to strengthen SSH security, such as limiting access, enforcing strong authentication, and other best practices.
We want to run OS hardening on Server01 from DesktopTest ubuntu

At first we must install ansible on the DesktopTest :
```
sudo apt-add-repository ppa:ansible/ansible

sudo apt update

sudo apt install ansible

ansible --version
```
Note : python must be installed at any linux and consider that we have one linux for source (ansible management node) where the ansible is installed on it and one or many linux for destination that must be harden by ansible.

Important : management node must can ssh to any destination node passwordlesslly.

for this reason, we generate a ssh key on management node and copy it to any destination nodes.

on management node :
```
ssh-keygen -t rsa -b 4096
```
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/000.png?raw=true)

Generated keys at /root/.ssh :
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/00.png?raw=true)

Copy the public key (id_rsa.pub) to other nodes :
```
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.56.151
```
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/0.png?raw=true)

Check the public key on management and destination node …

management node (DesktopTest) :
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/1.png?raw=true)

destination node (Server01) :
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/2.png?raw=true)

Test the ssh login passwordlesslly :
```
ssh root@192.168.56.151
```
![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/4.png?raw=true)


