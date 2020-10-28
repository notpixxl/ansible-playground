# Ansible playground

## Vagrant

This repo is a playground for testing purpose only. You can find a Vagrantfile that will allow you to deploy X nodes.
The nodes knows each other, so you can install Ansible on your first node to make it your controller and use the other to test your playbooks.

Be aware that you have to create a ssh key on your controller and copy your pub key on other nodes

```bash
ssh-keygen -t ecdsa -b 521
ssh-add -l #use this to verify if ssh_agent is ready
eval `ssh-agent`#if ssh_agent isn't started
ssh-add
```

now you can ssh-copy-id on your X nodes.

## Docker

If you have docker on your env you can use the deploy.sh script, what it will do ? 
Create "fake" virtual machines with containers, sshd, systemd and python3 are installed and you can now test your playbook.

option for the script are :

* --create : launch x containers on linux
* --createwsl : launch x containers on wsl
* --drop : remove previously created containers
* --infos : display containers info on linux
* --infoswsl : display containers info on wsl
* --start : boot your containers
* --ansible : create a testing ansible directory
* --ansiblewsl : create a testing ansible directory wsl compatible.

```./deploy.sh --create 5```
Create 5 debian containers, create your user, make 'password' your default pass, copy your id_rsa.pub onto authorized_keys, add you to passwordless sudoers.

>[!WARNING]
>Docker containers can have problem to start on WSL, it's because directory /sys/fs/cgroup/systemd do not exist on wsl
>make sure to create it with mkdir /sys/fs/cgroup/systemd before starting your deploy.sh

# Cheat Sheet

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

## Test Vagrant SSH connection with another user

```bash
ssh -p 2222 username@localhost
```

## Init a new role 

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
