# ansible-atlassian-jira-application

Ansible role which installs and configures an Atlassian JIRA instance.

# Dependencies

## Oracle Java JDK

This role requires the Java JDK to be installed. The exact path can be
configured, see below for variables.

You can use `ansible-java-oracle-jdk` for this.

## Oracle Java default keystore

When using self signed certificates, it may be required to add those as trusted
certificates in the default keystore.

You can use `ansible-java-keystore` for this.

## RDBMS

A relational database is required for JIRA, e.g. MySQL, Postgres or
Oracle.

You can use `ansible-mysql-server` for for this.

## JDBC driver

JIRA needs a JDBC driver to connect to the database, which may not be
provided by the JIRA distribution. The exact path must be configured.

You can use `ansible-mysql-jdbc-driver` to install the MySQL JDBC driver.

## Packetfilter

It may be need to configure packetilter rules for incoming or outgoing network
connections.  

# Role Variables

Available variables are listed below, along with default values (see
`defaults/main.yml`).  

All variables have set sensible defaults and usually should not need any
configuration, The only variable required to be set is the path to the installed
JDK.  

## General settings

    jira_user_name: jira

User name under which the application will be installed. The account will be
created if it does not exist.  

    jira_group_name: jira

Group name under which the application will be installed. The group will be
created if it does not exist.

    jira_service_name: jira

Service name of the application.

    jira_provide_root_environment: false

Provide preconfigured aliases to the root shell for easier navigation to
JIRA-specific directories.

    jira_start_on_boot :false

Start JIRA automatically at boot.

Set this to true in production environments. Even better, use a process
supervisor e.g. systemd or monit.

## Installation variables

    jira_perform_installation: false

Install or upgrade Atlassian JIRA automatically.

Upgrade is the same as installation, but the previously installed version
will not be removed. If the configured JIRA version does not match the
current JIRA version on the host, the configured JIRA version
will be installed.

This flag can be used as an additional safety mechanism so the JIRA
installation is not accidentally changed. It could e.g. be enabled only in
a special, separate playbook for installations.

    jira_start_after_installation: false

Start JIRA instance after the role has finished an installation or an
upgrade.

This is handy if you want to restore a backup before the instance is started.

    jira_installation_archive_url: https://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-software-7.1.1-jira-7.1.1.tar.gz

HTTP URL where to get the tar.gz archive for the JIRA application.

Depending on the environment, a HTTP proxy configuration may be needed in
ansible.

    jira_installation_archive_name: atlassian-jira-software-7.1.1-standalone

Folder name of the extracted JIRA instance.

    jira_installation_archive_checksum: sha1:2ecb9442628c408e74bf22243cc7fa381ba73ff7

Checksum of the tar.gz archive for the given version.

    jira_config_software_directory: /opt/atlassian/jira

Path to a directory where the JIRA instance should be installed.

    jira_installation_jdbc_driver_jar_source_path: (empty)

Optional path to a JDBC driver jar file on the control machine that will be
copied into the JIRA instance lib directory on the remote machine.

## Configuration variables

    jira_config_jre_directory: ''

Required path to the directory that contains the Oracle JDK 1.8.

    jira_config_home_directory: /var/atlassian/application-data/jira

Path to the JIRA home directory.

    jira_config_jvm_heap_size: 1024m

How much memory the JVM should use.

This is passed to the `-xXs` and `-Xmx` JVM options.

    jira_config_jvm_opts: ''

Additional JVM configuration options.

    jira_config_tomcat_context_path: ''

The tomcat context path for the JIRA instance. By default, JIRA is
reachable at /.

    jira_config_tomcat_access_log: true

If tomcat should create access logs.

jira_config_tomcat_connectors:
    standalone:
        enabled: true
        type: standalone
        listening_port: 8080
    reverse_proxy:
        enabled: false
        type: proxy
        listening_port: 8081
        proxied_hostname: jira.example.org
        proxied_port: 443

Dictionary with tomcat connector configurations. The dictionary can contain an
arbitrary number of connectors with arbitrary names.

# Example Playbook

    - hosts: jira-servers
      roles:
         - role: ansible-atlassian-jira-application
           jira_config_jre_directory: '/usr/java/jdk1.8.0_72'

# License

MPLv2
