addadminusers
=========
Thiis role can be used to create admin users, add their pubkey and make them sudoers on a Linux Server

Requirements
------------
*sudo package
*users pubkey in files/ folder
*openssh-server up & running

Role Variables
--------------

vars/mail.yml : contain the admins username each of them correspond to a pubkey in files/

Dependencies
------------


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: notpixxl.addadminusers, x: 42 }

License
-------

BSD

Author Information
------------------

cpiveteau@groupe-idea.com
