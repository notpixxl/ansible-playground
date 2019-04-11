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
ansible default -i ./hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant -m ping-vvv
```

## Run a playbook
A playbook is a set of 'plays' each play is related to a hosts group and contains a tasks list to run. It is possible to run some hooks called handlers at the end of a tasks.

```bash
ansible-playbook ./playbooks.yml -i ./hosts --private-key=~/.vagrant.d/insecure_private_key -u vagrant
```

## Test Vagrant SSH connection with another user

```bash
ssh -p 2222 username@localhost
```



