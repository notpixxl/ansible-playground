Role Name
=========

This role can Manager Server entities configuration on a Netscaler.

Requirements
------------

* nitro python sdk

Role Variables
--------------

* Vault.yml: The vault file contain any credentials that are required to connect to the netscaler appliance.
* Host_vars hostfile: This file contain any variables (hostname, vserver IP, VSLB IP, Monitor Options etc ...) that are required to declare the vserver on the netscaler appliance

Dependencies
------------

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: webservers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

<cpiveteau@groupe-idea.com>
