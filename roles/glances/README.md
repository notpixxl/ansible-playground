Role Name
=========

This role allowed to install and configure glances.

Requirements
------------

This roles should be used to provision Debian 9 or Ubuntu 18.04 target.
It can be runned conditionnaly:
```yaml
- hosts: target
  tasks:
  - include_role:
      name: glances
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
    name: glances
  when: "ansible_facts['os_family'] == 'Debian'"
```

License
-------

IDEA

Author Information
------------------

Christophe PIVETEAU <cpiveteau@groupe-idea.com>