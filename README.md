# Tomcat-Ansible-CentOS8
Ansible role for deploying Tomcat with configured Session Replication on CentOS8

# 

# Role Variables
Main variable (defaults/main.xml):
1
2
3
- `tomcat_ver`: Tomcat version to install, Default is "9.0.52".
- `tomcat_setup_folder`: Location in which tomcat will be installed. Default is "/var/lib/tomcat".

```yaml
tomcat_ver: 9.0.52
tomcat_archive_url: https://archive.apache.org/dist/tomcat/tomcat-9/v{{ tomcat_ver }}/bin/apache-tomcat-{{ tomcat_ver }}.tar.gz
tomcat_archive_dest: /tmp/apache-tomcat-{{ tomcat_ver }}.tar.gz
tomcat_setup_folder: /var/lib/tomcat
```
#

Tomcat UI Credentials variables (vars/tomcat-secrets.yml):
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
# Session Replication Settings (templates/server.xml):
```xml
<Engine name="Catalina" defaultHost="localhost" jvmRoute="{{ new_hostname }}">


          <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
             channelSendOptions="9">

      <Manager className="org.apache.catalina.ha.session.DeltaManager"
               expireSessionsOnShutdown="false"
               notifyListenersOnReplication="true"/>

      <Channel className="org.apache.catalina.tribes.group.GroupChannel">
        <Membership className="org.apache.catalina.tribes.membership.McastService"
                    address="228.0.0.5"
                    port="45564"
                    frequency="500"
                    dropTime="3000"/>
        <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                  address="{{ ansible_ssh_host }}"
                  port="4000"
                  autoBind="100"
                  selectorTimeout="5000"
                  maxThreads="6"/>

        <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
          <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
        </Sender>
        <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
        <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatchInterceptor"/>
      </Channel>

      <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
             filter=".*\.gif|.*\.js|.*\.jpeg|.*\.jpg|.*\.png|.*\.htm|.*\.html|.*\.css|.*\.txt"/>
      <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

      <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                tempDir="/tmp/war-temp/"
                deployDir="/tmp/war-deploy/"
                watchDir="/tmp/war-listen/"
                watchEnabled="false"/>

      <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
    </Cluster>
```

- `ansible_ssh_host`: host ipv4 adress (gets automatically)
