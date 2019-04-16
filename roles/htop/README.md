Role Name
=========

This role allowed to install and configure htop.

Requirements
------------

This roles should be used to provision Debian 9 target.
It can be runned conditionnaly:
```yaml
- hosts: target
  tasks:
  - include_role:
      name: htop
    when: "ansible_facts['os_family'] == 'Debian'"
```

Role Variables
--------------

No Variables

Dependencies
------------

No dependencies.

Example Playbook
----------------

```yaml
  - include_role:
    name: htop
  when: "ansible_facts['os_family'] == 'Debian'"
```

License
-------

IDEA

Author Information
------------------

Christophe PIVETEAU <cpiveteau@groupe-idea.com>
