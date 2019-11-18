Role Name
=========

This role will install and configure Keycloak server.

Requirements
------------

There is no such requirements for the role.

Role Variables
--------------

- username: username for administartion
- password: password for administartion

Dependencies
------------

Role required pre-installation of java

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: keycloak, x: 42 }

License
-------



Author Information
------------------

Abhishek Vishwakarma (abhishek.vishwakarma@opstree.com).
