# Tomcat-Ansible-CentOS8
Ansible role for deploying Tomcat with configured Session Replication on CentOS8

# 

# Role Variables
Main variable:
- `tomcat_ver`: Tomcat version to install, Default is "9.0.52".
- `tomcat_setup_folder`: Location in which tomcat will be installed. Default is "/var/lib/tomcat".

```yaml
tomcat_ver: 9.0.52
tomcat_archive_url: https://archive.apache.org/dist/tomcat/tomcat-9/v{{ tomcat_ver }}/bin/apache-tomcat-{{ tomcat_ver }}.tar.gz
tomcat_archive_dest: /tmp/apache-tomcat-{{ tomcat_ver }}.tar.gz
tomcat_setup_folder: /var/lib/tomcat
```
#

Tomcat UI Credentials variables:
- `ui_manager_user`: Default is "manager".
- `ui_manager_pass`: Default is "1111".
- `ui_admin_username`: Default is "admin".
- `ui_admin_pass`: Default is "manager".

```yaml
ui_manager_user: manager
ui_manager_pass: 1111
ui_admin_username: admin
ui_admin_pass: 1111
```
#

# Host File
```txt
[tomcat_servers]
node1 ansible_host=0.0.0.0 new_hostname=node1 new_server_port=8080
node2 ansible_host=0.0.0.0 new_hostname=node2 new_server_port=8081
```
