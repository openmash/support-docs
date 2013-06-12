Install Tomcat7
===============

{:toc}

# On Ubuntu 12.04 #

    sudo apt-get install tomcat7

* edit JAVA_HOME /etc/default/tomcat7 

The default installation of Tomcat 7 for Linux Debian is listening on port 8080.

When you want to change the port to 80 then you have several options:

1. You can use iptables and redirect communication from port 8080 to port 80.

2. The more straight forward approach is to bind Tomcat directly to port 80. First of all change port 8080 to 80 in file /etc/tomcat7/server.xml.

You’ll see error messages in /var/log/tomcat7/catalina.out when you try to restart Tomcat:

    SEVERE: Failed to initialize connector [Connector[HTTP/1.1-80]]
    org.apache.catalina.LifecycleException: Failed to initialize component [Connector[HTTP/1.1-80]]
    Caused by: java.net.BindException: Permission denied

The problem is that default installation of Tomcat 7 for Linux Debian allows to bind only ports higher     than 1023. You need to allow binding to privileged ports.

* Open file /etc/defaults/tomcat7 and change option from:

    `#AUTHBIND=no`

to:

    `AUTHBIND=yes`

* Restart Tomcat and it will listen on port 80.

* Deploy .war files by copy to `/var/lib/tomcat7/webapps`

## Install admin apps ##

    sudo apt-get install tomcat7-admin

* edit the users file 

    $ sudo nano /etc/tomcat7/tomcat-users.xml 

    ...
    <role rolename="tomcat"/>
    <role rolename="role1"/>
    <role rolename="manager-gui"/>
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <user username="tomcat" password="secret" roles="manager-gui,tomcat"/>
    <user username="tadmin" password="secret" roles="admin-gui"/>
    <user username="sadmin" password="secret" roles="admin-script"/>
    <user username="both" password="secret" roles="tomcat,role1"/>
    <user username="role1" password="secret" roles="role1"/>
    ...

# Install Tomcat7 on RHEL/CentOS #

## Install Java (7.0._13) ##

Find what OS type we have

    # uname -a
    Linux al-e8wc6wpm 2.6.32-279.14.1.el6.x86_64 #1 SMP Mon Oct 15 13:44:51 EDT 2012 x86_64 x86_64 x86_64 GNU/Linux

That means we have x86_64 linux system. 

We will install Tomcat 7 under `/opt`. From `http://tomcat.apache.org/download-70.cgi` download core tar.gz:

    # cd /opt
    # wget -c http://mirrors.hostingromania.ro/apache.org/tomcat/tomcat-7/v7.0.41/bin/apache-tomcat-7.0.41.tar.gz
    # md5sum apache-tomcat-7.0.41.tar.gz
    cca4176d9901ca1300a56390c36fd6f0  apache-tomcat-7.0.41.tar.gz
    
    # tar -xvf apache-tomcat-7.0.41.tar.gz
    # cd apache-tomcat-7.0.41
    # ln -s /opt/apache-tomcat-7.0.41 /opt/tomcat

## Configure Tomcat to Run as a Service ##

We will now see how to run Tomcat as a service and create a simple Start/Stop/Restart script, as well as to start Tomcat at boot.

Setup log directory 

    # ln -s /opt/tomcat/logs /var/log/tomcat

Create the default tomcat configuration at /etc/default/tomcat7:

    # Run Tomcat as this user ID. Not setting this or leaving it blank will use the
    # default of tomcat7.
    TOMCAT7_USER=tomcat7
    
    # Run Tomcat as this group ID. Not setting this or leaving it blank will use
    # the default of tomcat7.
    TOMCAT7_GROUP=tomcat7
    
    CATALINA_OPTS=-Dlog.directory=/opt/tomcat/logs
    
    # The home directory of the Java development kit (JDK). You need at least
    # JDK version 1.5. If JAVA_HOME is not set, some common directories for
    # OpenJDK, the Sun JDK, and various J2SE 1.5 versions are tried.
    #JAVA_HOME=/usr/lib/jvm/openjdk-6-jdk
    
    # You may pass JVM startup parameters to Java here. If unset, the default
    # options will be: -Djava.awt.headless=true -Xmx128m -XX:+UseConcMarkSweepGC
    #
    # Use "-XX:+UseConcMarkSweepGC" to enable the CMS garbage collector (improved
    # response time). If you use that option and you run Tomcat on a machine with
    # exactly one CPU chip that contains one or two cores, you should also add
    # the "-XX:+CMSIncrementalMode" option.

    JAVA_OPTS="-Djava.awt.headless=true -XX:+UseConcMarkSweepGC"
    JAVA_OPTS="${JAVA_OPTS} -Xmx2048m -XX:MaxPermSize=512m"

    
    # To enable remote debugging uncomment the following line.
    # You will then be able to use a java debugger on port 8000.
    #JAVA_OPTS="${JAVA_OPTS} -Xdebug -Xrunjdwp:transport=dt_socket,address=8000,server=y,suspend=n"
    
    # Java compiler to use for translating JavaServer Pages (JSPs). You can use all
    # compilers that are accepted by Ant's build.compiler property.
    #JSP_COMPILER=javac
    
    # Use the Java security manager? (yes/no, default: no)
    #TOMCAT7_SECURITY=no
    
    # Number of days to keep logfiles in /var/log/tomcat7. Default is 14 days.
    #LOGFILE_DAYS=14
    
    # Location of the JVM temporary directory
    # WARNING: This directory will be destroyed and recreated at every startup !
    #JVM_TMP=/tmp/tomcat7-temp
    
    # If you run Tomcat on port numbers that are all higher than 1023, then you
    # do not need authbind.  It is used for binding Tomcat to lower port numbers.
    # NOTE: authbind works only with IPv4.  Do not enable it when using IPv6.
    # (yes/no, default: no)
    AUTHBIND=yes

Use *AUTHBIND=yes*, to allow tomcat port mapping to run on port less than 1024 (e.g. 80)

Change to the `/etc/init.d` directory and create a script called `tomcat7` as shown below. 

    # cd /etc/init.d
    # touch tomcat7
    # vi /etc/init.d/tomcat7
    
    #!/bin/bash
    # description: Tomcat Start Stop Restart
    # processname: tomcat
    # chkconfig: 234 20 80
    
    JAVA_HOME=/opt/java
    export JAVA_HOME
    PATH=$JAVA_HOME/bin:$PATH
    
    export PATH
    CATALINA_HOME=/opt/tomcat
    
    case $1 in
    start)
    sh $CATALINA_HOME/bin/startup.sh
    ;;
    stop)
    sh $CATALINA_HOME/bin/shutdown.sh
    ;;
    restart)
    sh $CATALINA_HOME/bin/shutdown.sh
    sh $CATALINA_HOME/bin/startup.sh
    ;;
    esac
    exit 0

Make it executable 

    # chmod 755 /etc/init.d/tomcat7

Configure Tomcat7 port to run onto (don't forget about that AUTHBIND=yes) 

    # vi /opt/tomcat/conf/server.xml

edit

    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

to

    <Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="443" />

We now use the chkconfig utility to have Tomcat start at boot time. In my script above, I am using `chkconfig: 234 20 80`. `2345` are the run levels and `20` and `80` are the stop and start priorities respectively. You can adjust as needed. 

    # chkconfig --add tomcat7
    # chkconfig --level 234 tomcat7 on

Verify it:

    # chkconfig --list tomcat7
    tomcat7          0:off   1:off   2:on    3:on    4:on    5:off   6:off

Now, let's test our script. 

Start Tomcat:

    # service tomcat7 start
    Using CATALINA_BASE:   /opt/tomcat
    Using CATALINA_HOME:   /opt/tomcat
    Using CATALINA_TMPDIR: /opt/tomcat/temp
    Using JRE_HOME:        /opt/java
    Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar

Stop Tomcat:

    # service tomcat7 stop
    Using CATALINA_BASE:   /opt/tomcat
    Using CATALINA_HOME:   /opt/tomcat
    Using CATALINA_TMPDIR: /opt/tomcat/temp
    Using JRE_HOME:        /opt/java
    Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar

Restarting Tomcat (Must be started first):

    # service tomcat7 restart
    Using CATALINA_BASE:   /opt/tomcat
    Using CATALINA_HOME:   /opt/tomcat
    Using CATALINA_TMPDIR: /opt/tomcat/temp
    Using JRE_HOME:        /opt/java
    Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
    Using CATALINA_BASE:   /opt/tomcat
    Using CATALINA_HOME:   /opt/tomcat
    Using CATALINA_TMPDIR: /opt/tomcat/temp
    Using JRE_HOME:        /opt/java
    Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar

We should review the `catalina.out` log located at `/var/log/tomcat/catalina.out` and check for any errors.

We can now access the Tomcat Manager page at: `http://yourdomain.com:80` or `http://yourIPaddress:80` and we should see the Tomcat home page. 

## Configure Tomcat Manager Access ##

Tomcat 7 contains a number of changes that offer finer-grain roles.

For security reasons, no users or passwords are created for the Tomcat manager roles by default. In a production deployment, it is always best to remove the Manager application.

To set roles, user name(s) and password(s), we need to configure the tomcat-users.xml file located at `$CATALINA_HOME/conf/tomcat-users.xml`.

In the case of our installation, `$CATALINA_HOME` is located at /opt/apache-tomcat-7.0.41.

By default the Tomcat 7 `tomcat-users.xml` file will have the elements between the and tags commented-out.

New roles for Tomcat 7 offer finer-grained access and The following roles are now available: 

    manager-gui
    manager-status
    manager-jmx
    manager-script
    admin-gui
    admin-script

We can set the manager-gui role, for example as below 

    <role rolename="tomcat"/>
    <role rolename="role1"/>
    <role rolename="manager-gui"/>
    <role rolename="manager-status">
    <role rolename="manager-jmx">
    <role rolename="manager-script">
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <user username="tomcat" password="secret" roles="tomcat,manager-gui,manager-status,manager-jmx,manager-script"/>
    <user username="tadmin" password="secret" roles="admin-gui"/>
    <user username="sadmin" password="secret" roles="admin-script"/>
    <user username="both" password="secret" roles="tomcat,role1"/>
    <user username="role1" password="secret" roles="role1"/>

## Restart Tomcat ##

Manage Memory Usage Using `JAVA_OPTS`

Getting the right heap memory settings for your installation will depend on a number of factors. For simplicity, we will set our inital heap size, `Xms`, and our maximum heap size, `Xmx`, to the same value of 128 Mb.

Simliarly, there are several approaches you can take as to where and how you set your `JAVA_OPTS`. Again, for simplicity, we will add our `JAVA_OPTS` memory parameters in our `catalina.sh` file. So, open the `catalina.sh` file located under `/opt/tomcat/bin/catalina.sh` with a text editor or `vi`. Since we are using 128 Mb for both initial and maximum heap size, add the following line to `catalina.sh`:

    # vi /opt/tomcat/bin/catalina.sh
    JAVA_OPTS="$JAVA_OPTS -Xms128m -Xmx128m"

## How to Run Tomcat using Minimally Privileged (non-root) User ##

> `Note: be sure you have installed and configured authbind`

In our Tomcat configuration above, we are running Tomcat as Root. For security reasons, it is always best to run services with the only those privileges that are necessary. There are some who make a strong case that this is not required, but it's always best to err on the side of caution. To run Tomcat as non-root user, we need to do the following:

Create the group `tomcat`: 

    # groupadd tomcat

Create the user `tomcat` and add this user to the tomcat group we created above. 

    # useradd -g tomcat -d /opt/apache-tomcat-7.0.41 tomcat
    # chown -Rf tomcat:tomcat /opt/apache-tomcat-7.0.41

>   `Note: it is possible to enhance our security still further by making certain files and directories read-only. This will not be covered in this post and care should be used when setting such permissions.`

Adjust the start/stop service script we created above. In our new script, we need to su to the user tomcat:

    # description: Tomcat Start Stop Restart  
    # processname: tomcat  
    # chkconfig: 234 20 80  
    JAVA_HOME=/usr/java/jdk1.7.0_05  
    export JAVA_HOME  
    PATH=$JAVA_HOME/bin:$PATH  
    export PATH  
    CATALINA_HOME=/usr/share/apache-tomcat-7.0.29/bin  
      
    case $1 in  
    start)  
    /bin/su tomcat $CATALINA_HOME/startup.sh  
    ;;   
    stop)     
    /bin/su tomcat $CATALINA_HOME/shutdown.sh  
    ;;   
    restart)  
    /bin/su tomcat $CATALINA_HOME/shutdown.sh  
    /bin/su tomcat $CATALINA_HOME/startup.sh  
    ;;   
    esac      
    exit 0