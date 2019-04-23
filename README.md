# Ansible playground

## Requirements
* Have Vagrant/Virtual box installed on your host machine
* Have ansible installed on your host machine
* You should add your public ssh key to the authorized_key file before doing anything.

## Ping vagrant with ansible
* default is referencing the target hosts. Defined in the inventory file.
* -i point to the inventory file
* --private-key points to vagrant specific private key
* -u specify user for ssh
* -m module to execute
* ping module is not a classic ping. It performs a ping via ssh

```bash
ansible db1 -i ./hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant -m ping -vvv
```

## Run a playbook
A playbook is a set of 'plays' each play is related to a hosts group and contains a tasks list to run. It is possible to run some hooks called handlers at the end of a tasks.

```bash
ansible-playbook ./idea_provisionning.yml -i ./hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant
```

## Test Vagrant SSH connection with another user

```bash
ssh -p 2222 username@localhost
```
## useradd_ideaadmins Playbook
* Modify group_vars/staff.yml ans add your new admin username
* Add the username.key.pub to files directory
* Run the playbook

```bash
ansible-playbook useradd_ideaadmins_with_ssh.yml -i hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant
```

## Init a new role repository
```bash
ansible-galaxy init nom_du_role --init-path=./roles --offline
```

## Add package to IDEA provisionning
* The playbook idea_provisionning.yml is base on a list of packages installed with no configuration
* To add a new package to the list please add it to the list in group_vars/idea_dependencies.yml
* Please be warned that this list is only for package that don't need a custom conf, if you need to configure a package please make a role instead.

## mySQL Provisionning
This playbook is based on ansible-galaxy geerlingguy's playbook, some modifications as been made on the files defaults/main.yml of the role to feet our environnement. To note include password in this file you can add a variable hold by a vault file.
###Requirements