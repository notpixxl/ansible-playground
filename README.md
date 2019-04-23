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
This playbook is based on ansible-galaxy geerlingguy's playbook <https://github.com/geerlingguy/ansible-role-mysql>, some modifications have been made on the defaults/main.yml role file to feet our environnement. Include clear password in this file is prohibited you can add a variable hold by vault.yml vault file.

* To add a new user to mysql provisionning add the it in the mysql_user section 
```yml
mysql_users:
#   - name: example
#     host: 127.0.0.1
#     password: secret
#     priv: *.*:USAGE
    - name: cpiveteau
      host: "%"
      password: '{{ cpiveteau_database_password }}'
      priv: "*.*:ALL"
```
* Modify the vault.yml file, you have to know the vault password
```bash
ansible-vault edit vault.yml
```
* Run the playbook with the prompt switch to demand for vault password 
```bash
ansible-playbook --vault-id @prompt mysql.yml -i hosts
```