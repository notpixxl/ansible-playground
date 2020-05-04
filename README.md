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

## Install a role from Github

```bash
ansible-galaxy install git+git@github.com:notpixxl/ansible-ocsinventory-agent.git
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

## Generate a Hashed password

Ansible ad-hoc command is the easiest option:

```bash
ansible all -i localhost, -m debug -a "msg={{ 'mypassword' | password_hash('sha512', 'mysecretsalt') }}"
```

The mkpasswd utility that is available on most Linux systems is also a great option:

```bash
mkpasswd --method=sha-512
```

If this utility is not installed on your system (e.g. you are using macOS) then you can still easily generate these passwords using Python. First, ensure that the Passlib password hashing library is installed:

```bash
pip install passlib
```

Once the library is ready, SHA512 password values can then be generated as follows:

```bash
python -c "from passlib.hash import sha512_crypt; import getpass; print(sha512_crypt.using(rounds=5000).hash(getpass.getpass()))"
```

Use the integrated Hashing filters to generate a hashed version of a password. You shouldn’t put plaintext passwords in your playbook or host_vars; instead, use Using Vault in playbooks to encrypt sensitive data.