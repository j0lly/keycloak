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
                            {{ keycloak_version }}/\
                            {{ keycloak_download_file }}"
    keycloak_download_dir: /tmp

    keycloak_ext: <extension module="org.keycloak.keycloak-server-subsystem"/>
    keycloak_subsystem: '
        <subsystem xmlns="urn:jboss:domain:keycloak-server:1.1">
            <web-context>auth</web-context>
        </subsystem>'

    keycloak_default_ds: '
        <datasource jndi-name="java:jboss/datasources/KeycloakDS"
        pool-name="KeycloakDS" enabled="true" use-java-context="true">
            <connection-url>
                jdbc:h2:${jboss.server.data.dir}/keycloak;AUTO_SERVER=TRUE
            </connection-url>
            <driver>h2</driver>
            <security>
                <user-name>sa</user-name>
                <password>sa</password>
            </security>
        </datasource>'

    keycloak_ds_driver_url: ""
    keycloak_ds_driver_name: ""
    keycloak_ds_driver_path: "{{ wildfly_dir }}/modules/org/\
                            {{ keycloak_ds_driver_name }}/main"
    keycloak_ds_driver_module: ""
    keycloak_custom_driver: ""

    keycloak_custom_ds: ""

By default, this role will not install any driver and just the default
datasource. Here's and example on how to setup the variables to install a
custom driver and datasource (in this case, for PostgreSQL):

    keycloak_ds_driver_url: "https://jdbc.postgresql.org/download/\
                            postgresql-9.4-1204.jdbc42.jar"
    keycloak_ds_driver_name: "postgresql"
    keycloak_ds_driver_module: '
        <?xml version="1.0" ?>
        <module xmlns="urn:jboss:module:1.1" name="org.postgresql">

            <resources>
                <resource-root path="postgresql-9.4-1204.jdbc42.jar"/>
            </resources>

            <dependencies>
                <module name="javax.api"/>
                <module name="javax.transaction.api"/>
            </dependencies>
        </module>'
    keycloak_custom_driver: '
        <driver name="postgresql" module="org.postgresql">
            <xa-datasource-class>
                org.postgresql.xa.PGXADataSource
            </xa-datasource-class>
            <datasource-class>org.postgresql.Driver</datasource-class>
        </driver>'
    keycloak_custom_ds: '
        <datasource jta="true" jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-ccm="true">
            <connection-url>jdbc:postgresql://localhost:5432/keycloak</connection-url>
            <driver-class>org.postgresql.Driver</driver-class>
            <driver>postgresql</driver>
            <security>
                <user-name>keycloak</user-name>
                <password>keycloak</password>
            </security>
            <validation>
                <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker"/>
                <background-validation>true</background-validation>
                <exception-sorter class-name="org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter"/>
            </validation>
        </datasource>'

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
