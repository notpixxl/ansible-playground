# Ansible playground

## Requirements
* Have Vagrant/Virtual box installed on your host machine
* Have ansible installed on your host machine
* You should add your public ssh key to the authorized_key file before doing anything.

## Vagrant Test Box
If you want to test your playbook on Vagrant test box you first need to copy your ssh key on vagrant user 

```bash
ssh-copy-id vagrant@127.0.0.1 -p 2222
```
'vagrant' is the defaut password for all the boxes

## Ping vagrant with ansible
* default is referencing the target hosts. Defined in the inventory file.
* -i point to the inventory file
* --private-key points to vagrant specific private key
* -u specify user for ssh
* -m module to execute
* ping module is not a classic ping. It performs a ping via ssh

```bash
ansible all -i ./hostfile_vagrant_playground --private-key=~/.vagrant.d/insecure_private_key -u vagrant -m ping -vvv
```
## Extract Ansible Facts from an host 
To gather all ansible_facts from an host you can run this command 

```bash
ansible all -m setup -i hostfile_vagrant_playground > ansible_facts_hostfile_vagrant_playground.txt
```

This will create a file on your repo with all the usable facts for your Guests servers

```xml
        }, 
        "ansible_distribution": "Ubuntu", 
        "ansible_distribution_file_parsed": true, 
        "ansible_distribution_file_path": "/etc/os-release", 
        "ansible_distribution_file_variety": "Debian", 
        "ansible_distribution_major_version": "18", 
        "ansible_distribution_release": "bionic", 
        "ansible_distribution_version": "18.04",
```

## Run a playbook
A playbook is a set of 'plays' each play is related to a hosts group and contains a task list to run. It is possible to run some hooks called handlers at the end of a task.

```bash
ansible-playbook ./plbk_idea_provisionning.yml -i ./hostfile_vagrant_playground --private-key=~/.vagrant.d/insecure_private_key -u vagrant
```
## Test Vagrant SSH connection with another user

```bash
ssh -p 2222 username@localhost
```

## Init a new role repository
```bash
ansible-galaxy init nom_du_role --init-path=./roles --offline
```

## addadminusers role
* Modify roles/addadminusers/vars/main.yml ans add your new admin username
* Add the roles/addadminusers/files/username.key.pub to files directory
* Run the playbook

```bash
ansible-playbook plbk_useradd_ideaadmins_with_ssh.yml -i hostfile_vagrant_playground --private-key=~/.vagrant.d/insecure_private_key -u vagrant
```

## Add package to IDEA provisionning
* The playbook plbk_idea_provisionning.yml is base on a list of packages installed with no configuration
* To add a new package to the list please add it to the list in group_vars/idea_dependencies.yml
* Please be warned that this list is only for package that don't need a custom conf, if you need to configure a package please make a role instead.

## mySQL Provisionning
mysql.yml playbook is based on ansible-galaxy geerlingguy's playbook <https://github.com/geerlingguy/ansible-role-mysql>, some modifications have been made on the defaults/main.yml role file to feet our environnement. Include clear password in this file is prohibited you can add a variable hold by vault.yml vault file.
```yml
mysql_databases: []
```
The MySQL databases to create. A database has the values name, encoding (defaults to utf8), collation (defaults to utf8_general_ci) and replicate (defaults to 1, only used if replication is configured). The formats of these are the same as in the mysql_db module.

You can also delete a database (or ensure it's not on the server) by setting state to absent (defaults to present).
```yml
mysql_users: []
```
The MySQL users and their privileges. A user has the values:

    * name
    * host (defaults to localhost)
    * password (can be plaintext or encrypted—if encrypted, set encrypted: yes)
    * encrypted (defaults to no)
    * priv (defaults to *.*:USAGE)
    * append_privs (defaults to no)
    * state (defaults to present)

The formats of these are the same as in the mysql_user module.

* To add a new database to mysql servers add it to the mysql_databases section 

```yml
mysql_databases:
#   - name: example
#     collation: utf8_general_ci
#     encoding: utf8
#     replicate: 1
    - name: testdb
      collation: utf8_general_ci
      encoding: utf8
      replicate: 1
```      

* To add a new user to mysql provisionning add it to the mysql_user section 
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
* Run the playbook with the prompt switch to ask for vault password 
```bash
ansible-playbook --vault-id @prompt plbk_mysql.yml -i hostfile_vagrant_playground
```
## Connect to mysql via Vagrant
You can ssh to your vagrant Host and try  to connect with root account 
```bash
vagrant ssh ndb0
sudo mysql -u root -p
```
> Note: If you get an error like ERROR 1698 (28000): Access denied for user 'root'@'localhost' (using password: YES) when trying to log in from the CLI you might need to run as root or sudoer.

## Test vagrant mysql service from host
* Append a new line on vagrant file on your sql server for forward 3306 port to host 
```bash
config.vm.network "forwarded_port", guest: 3306, host: 3306, id: "mysql"
```
* To connect to your service from your host 
```bash
mysql -h 127.0.0.1 -P 3306 -u mysql_user -p
```

## Ansible Vault
* Because Ansible tasks, handlers, and other objects are data, these can also be encrypted with vault. If you’d like to not expose what variables you are using, you can keep an individual task file entirely encrypted.
* Vault key is asked when you run the mysql playbook
* Vault.yml contain all db_users password

To create a new vault :
```bash
ansible-vault create vault.yml
```
To edit a vault :
```bash
ansible-vault edit vault.yml
```
To change a vault key (previous key is needed)
```bash
ansible-vault rekey vault.yml
```

## Centreon Playbook

* this playbook permit you to add a list of Ubuntu Linux 18.04 severs into our monitoring engine
* you have to run plbk_idea_provisionning.yml before to configure snmpd on your servers
* you have to create a hostfile with this structure

```yml
[supervision]
srv-ces ansible_ssh_host=10.99.99.240 ansible_ssh_port=22
[newservers]
svlxappvm08 ansible_ssh_host=10.99.98.158 ansible_ssh_port=22 ansible_python_interpreter=/usr/bin/python3
svlxappvm09 ansible_ssh_host=10.99.98.159 ansible_ssh_port=22 ansible_python_interpreter=/usr/bin/python3
```
* the playbook will add your host with local ip, configure snmp_community, simple ping command, notifications period, and add it to the Linux-servers hostgroup
* the playbook will execute POLLERGENERATE, POLLERTEST, CFGMOVE and POLLERRESTART at the end so your probes are directly active
* All the sources and options came from <https://documentation-fr.centreon.com/docs/centreon/en/2.7.x/api/clapi/engine_management.html> and <https://www.monitoring-fr.org/2015/05/automatisation-de-la-supervision-exemple-avec-centreon-et-ansible-2/>

> if you test CLAPI directly from the centreon server you add to execute command as sudo

> Todo : 
> * Turn this playbook in a role 
> * Identify ditributions and installed packages on servers to add to specific hostgroup with specific servicegroup in centreon, example, webservers with apache go to Linux-Servers hostgroup and Apache-Webservers hostgroup with default probes on Linux-Servers and Apache specific probes for Apache-Webservers.

To Run the playbook, Vault pass is in keepass, SUDO pass is yours

```bash
ansible-playbook --check -i hostfile_keycloak_centreon_add --vault-id @prompt plbk_centreon_add_unbuntu1804_servers.yml --ask-become-pass
```
