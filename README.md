Role Name
=========

This role will install and configure Keycloak server.

Requirements
------------

There is no such requirements for the role.

Role Variables
--------------

We are using below mention variables in this role.

|**Variables**| **Default Values**|**Description**|
| keyclaok_version | 6.0.1 | Define version of keycloak |
| base_url | https://downloads.jboss.org/keycloak | Base url to download keycloak |
| version_url | /6.0.1/keycloak-6.0.1.tar.gz | Version config to download keycloak |
| keycloak_url | base_url+version_url | Url to download keycloak |
| username | root | username for administartion |
| password | root123 | password for administartion |

Dependencies
------------

Role required pre-installation of java

## Inventory
----------------

An inventory should look like this:-
```ini
[keycloakhost]                 
keycloakhost1_ip    ansible_user=ubuntu
keycloakhost2_ip    ansible_user=centos
keycloakhost3_ip    ansible_user=opstree                      
```

Example Playbook
----------------

Here is an example playbook:-
```yml
---
- hosts: keycloakhost
  roles:
    - role: osm_keycloak
      become: yes
```

Usage
----------------

For using this role you have to execute playbook only
```shell
ansible-playbook -i hosts site.yml
```

License
-------



Author Information
------------------

**[Abhishek Vishwakarma](mailto:abhishek.vishwakarma@opstree.com)*
