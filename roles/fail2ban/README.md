Fail2ban role
=========

This role allowed to install and configure fail2ban.

Requirements
------------

This roles should be used to provision Debian 9 target.
It can be runned conditionnaly:
```yaml
- hosts: target
  tasks:
  - include_role:
      name: fail2ban
    when: "ansible_facts['os_family'] == 'Debian'"
```

Role Variables
--------------

```yaml
fail2ban_default_jail_findtime: 600 # Time during 
fail2ban_default_jail_maxretry: 5   # Nb of retry allowed during findtime
fail2ban_default_jail_bantime: 600  # Nb of seconds during an ip gets banned when maxretry is reach during findtime
```

Dependencies
------------

No dependencies.

Example Playbook
----------------

```yaml
  - include_role:
    name: fail2ban
  vars:
    fail2ban_default_jail_findtime: 500
    fail2ban_default_jail_maxretry: 3
    fail2ban_default_jail_bantime: 3600
  when: "ansible_facts['os_family'] == 'Debian'"
```
License
-------

IDEA

Author Information
------------------

Nicolas Le Borgne <nleborgne@groupe-idea.com>
