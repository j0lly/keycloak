Keycloak
=========

Installs the Keycloak integrated SSO and IDM.

Role Variables
--------------

The default variables works just fine for a development environment.

Defaults:

    keycloak_version: 1.8.1.Final

    keycloak_base_download_url: http://downloads.jboss.org/keycloak
    keycloak_name: keycloak-overlay-{{ keycloak_version }}
    keycloak_download_file: "{{ keycloak_name }}.tar.gz"
    keycloak_download_url: "{{ keycloak_base_download_url }}/\
                            {{ keycloak_version }}/\
                            {{ keycloak_download_file }}"
    keycloak_download_dir: /tmp

    # Standalone strings
    keycloak_ext: <extension module="org.keycloak.keycloak-server-subsystem"/>
    keycloak_subsystem: '
        <subsystem xmlns="urn:jboss:domain:keycloak-server:1.1">
            <web-context>auth</web-context>
        </subsystem>'
    keycloak_cache_container: '
        <cache-container name="keycloak" jndi-name="infinispan/Keycloak">
            <local-cache name="realms"/>
            <local-cache name="users"/>
            <local-cache name="sessions"/>
            <local-cache name="offlineSessions"/>
            <local-cache name="loginFailures"/>
        </cache-container>'
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

    # Drive and datasource string
    keycloak_ds_driver_url: ""
    keycloak_ds_driver_name: ""
    keycloak_ds_driver_path: "{{ wildfly_dir }}/modules/org/\
                            {{ keycloak_ds_driver_name }}/main"
    keycloak_ds_driver_module: ""
    keycloak_custom_driver: ""

    keycloak_custom_ds: ""

    # Manually defined variables
    # keycloak_management_user: ""
    # keycloak_management_password: ""

By default, this role will not install any driver and just the default
datasource. Here's and example on how to setup the variables to install a
custom driver and datasource (in this case, for PostgreSQL):

    keycloak_ds_driver_url: "https://jdbc.postgresql.org/download/\
                             postgresql-9.4.1207.jar"
    keycloak_ds_driver_name: "postgresql"
    keycloak_ds_driver_module: '
        <?xml version="1.0" ?>
        <module xmlns="urn:jboss:module:1.1" name="org.postgresql">

            <resources>
                <resource-root path="postgresql-9.4.1207.jar"/>
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
            <datasource-class>org.postgresql.ds.PGPoolingDataSource</datasource-class>
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

Admin user
----------

The admin user task should only be executed once, the task will fail if an
admin user already exists so it's recomended that you define the
`keycloak_management_user` and `keycloak_management_password` in only one run
as in the example below:

    $ ansible-playbook main.yml --extra-vars "keycloak_management_user=admin keycloak_management_password=admin"

License
-------

BSD
