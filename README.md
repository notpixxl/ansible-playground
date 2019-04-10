# Ansible Provisionning IDEA

# Testing Purpose !!!Do not use it in production Env!!!

File /etc/ansible/hosts is modified to add test host at EOF

```
[test_db_servers]
svlxbddvm05.idea.lo ansible_user=administrateur ansible_ssh_pass=userpass
```
To test that your host is ok with Ansible

```
ansible -m ping all
```

Should respond

```
svlxbddvm05.idea.lo | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

# useradd_ideaadmins_with_ssh

```
ansible-playbook useradd_ideaadmins_with_ssh.yml --ask-become-pass
```
