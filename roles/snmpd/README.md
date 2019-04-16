Role Name
=========

This role allowed to install and configure snmpd.

Requirements
------------

This roles should be used to provision Debian 9 or Ubuntu 18.04 target.
It can be runned conditionnaly:
```yaml
- hosts: target
  tasks:
  - include_role:
      name: snmpd
    when: "ansible_facts['os_family'] == 'Debian'"
```

Role Variables
--------------

```yaml
snmpd_agentAddress: agentAddress udp:161,udp6:[::1]:161 # Listen for connections on all interfaces (both IPv4 *and* IPv6)
snmpd_community_string: rocommunity public  10.99.99.240/32   # Default access to basic system info
```

Dependencies
------------

No dependencies.

Example Playbook
----------------

```yaml
  - include_role:
    name: snmpd
  when: "ansible_facts['os_family'] == 'Debian'"
```

License
-------

IDEA

Author Information
------------------

Christophe PIVETEAU <cpiveteau@groupe-idea.com>