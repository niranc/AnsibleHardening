# Hardening Linux Server with Ansible ...

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/1_qpcT3MQIVgGPC35Sr2kaRw.webp?raw=true)


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

We use some of the rules from the Link [ansible-collection-hardening](https://github.com/dev-sec/ansible-collection-hardening).

then check the ansible project and files at management node :

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/0000.png?raw=true)

All the project files has presented at my [github](https://github.com/kayvansol/AnsibleHardening).

files on management node :

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/6.png?raw=true)

Edit the inventory file based on destination node(s) :
```
all:
  vars:
    ansible_user: root
    ansible_port: 22
  children:
    single-node:
      hosts:
        192.168.56.151
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/7.png?raw=true)

then ping all destination host(s) to be ok before running the ansible playbooks :
```
ansible all -m ping
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/8.png?raw=true)

then run the playbook :

```
ansible-playbook -i inventory/RahBia.yml playbooks/hardening.yml
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/9.png?raw=true)

if any error happens, check and correct it and run the playbook again

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/10.png?raw=true)

Note : ansible process is idempotent, an idempotent operation is one that has no additional effect if it is called more than once with the same input parameters.

```
ansible-playbook -i inventory/RahBia.yml playbooks/hardening.yml
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/11.png?raw=true)

for checking the hardening, install lynis on destination nodes :
```
apt install lynis
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/12.png?raw=true)

run the lynis to audit the node :
```
./lynis audit system
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/13.png?raw=true)

at final, check the grade (84 % at our case) :

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/14.png?raw=true)

and check any warnings and suggestions :

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/15.png?raw=true)

you can change the destination ssh port (to 8090 in our case) and run the playbook with that port :

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/16.png?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/15-1.png?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/17.png?raw=true)

```
ansible-playbook -i inventory/RahBia.yml playbooks/hardening.yml
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/18.png?raw=true)

```
ssh root@192.168.56.151 -p 8090
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/19.png?raw=true)

```
ss -nltp | grep ssh
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/20.png?raw=true)

