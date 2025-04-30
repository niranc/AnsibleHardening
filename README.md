# Hardening Linux Server with Ansible

1. At first we must install ansible on the DesktopTest :
```
sudo apt-add-repository ppa:ansible/ansible

sudo apt update

sudo apt install ansible

ansible --version
```

2. - We generate a ssh key on management node and copy it to any destination nodes.

```
ssh-keygen -t rsa -b 4096 #generated at /home/user/.ssh
```

- Copy the public key (id_rsa.pub) to other nodes :
```
ssh-copy-id -i /home/user/.ssh/id_rsa.pub user@192.168.56.151
```

![alt text](https://raw.githubusercontent.com/kayvansol/AnsibleHardening/refs/heads/main/img/0.png?raw=true)

- Test the ssh login passwordlesslly :
```
ssh user@192.168.56.151
```

3.- Disable password for sudo operation
```
sudo visudo
```
- Then add the 'user' to execute actions without password.
```
user ALL=(ALL) NOPASSWD: ALL
```
Then Ctrl O + Enter + Ctrl X to save it.


4. Edit the inventory file based on destination node(s) :
```
all:
  vars:
    ansible_user: user
    ansible_port: 22
  children:
    single-node:
      hosts:
        192.168.56.151
```


# Usage
ping all destination host(s) to be ok before running the ansible playbooks :
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

