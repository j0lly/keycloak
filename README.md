Keycloak
=========

Installs the Keycloak integrated SSO and IDM.

Role Variables
--------------

The default variables works just fine for a development environment.

Defaults:

    keycloak_version: 1.4.0.Final

    keycloak_base_download_url: http://downloads.jboss.org/keycloak
    keycloak_name: keycloak-overlay-{{ keycloak_version }}
    keycloak_download_file: "{{ keycloak_name }}.tar.gz"
    keycloak_download_url: "{{ keycloak_base_download_url }}/\
                            {{ keycloak_version }}/{{ keycloak_download_file }}"
    keycloak_download_dir: /tmp

Dependencies
------------

- [inkatze.wildfly](https://galaxy.ansible.com/list#/roles/4474)

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: inkatze.keycloak }

License
-------

BSD
