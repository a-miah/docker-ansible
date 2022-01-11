# Ansible playbook to launch EC2 instance on AWS

## Ansible controller set up on localhost
```
Vagrant.configure("2") do |config|
    # creating are Ansible controller
      config.vm.define "controller" do |controller|
        
       controller.vm.box = "bento/ubuntu-18.04"
       
       controller.vm.hostname = 'controller'
       
       controller.vm.network :private_network, ip: "192.168.33.12"

      # controller.vm.provision "shell", path: "./Provisions/controller_provision.sh"
       
       # config.hostsupdater.aliases = ["development.controller"] 
       
      end 
    end
```

1. Run `vagrant up`
2. `vagrant ssh controller`
3. `sudo apt-get update -y` and `sudo apt-get upgrade -y`
4. Install dependencies for Ansible

```
sudo apt-get update
  
sudo apt-get install software-properties-common
  
sudo apt-add-repository ppa:ansible/ansible
  
sudo apt-get update -y
  
sudo apt-get install ansible -y

```
5. Install python - `sudo apt-get install python`
6. Install boto - `pip3 install boto`

7. create hosts file in cd /etc/ansible/ - `sudo nano hosts`
8. [localhost] - in hosts file


## Ansible vault to secure AWS keys
1. Create folder to store AWS keys in cd /etc/ansible/ - `mkdir group_vars/all/`
2. `cd group_vars/all/`
3. `sudo nano pass.yml`
4. Copy AWS Access and Secret keys into the file in following format:
5. create ansible vault - `ansible-vault create group_vars/all/pass.yml`


## Ansible playbook to launch EC2 
- Image to use is AMI of Ubuntu 18:04lts
- SSH into EC2 instance to check if security
- Run command after creating playbook - `sudo ansible-playbook docker.yml --ask-vault-pass`

```
---
- hosts: localhost
  connection: local
  gather_facts: True
  become: true
  vars:
    ansible_python_interpreter: /usr/bin/python3

  tasks:
    - name: Provision instance(s)
      ec2:
        aws_access_key: {aws_access_key}
        aws_secret_key: {aws_secret_key}
        assign_public_ip: true
        key_name: eng99
        vpc_subnet_id: subnet-00475307ab65e1c99
        group_id: sg-083289afc1dff1baf
        image: ami-07d8796a2b0f8d29c
        instance_type: t2.micro
        region: eu-west-1
        wait: yes
        count: 1
        instance_tags:
          Name: eng99_attaik_ansible_instance
```