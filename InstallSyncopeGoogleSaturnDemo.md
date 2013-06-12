Syncope Google Saturn Demo
===============

# Install #

## Prerequisites ##

### Install mysql ###

    # yum install mysql
    # yum install mysql-server
    # service mysqld start 
    
### Setup demo database ###

    # mysql -u root -p
    mysql> CREATE DATABASE IF NOT EXISTS syncope;
    Query OK, 1 row affected (0.00 sec)
    mysql> CREATE USER 'syncope'@'localhost' IDENTIFIED BY '<password>';
    Query OK, 0 rows affected (0.00 sec)
    mysql> GRANT ALL PRIVILEGES ON syncope.* TO 'syncope'@'localhost' WITH GRANT OPTION;
    Query OK, 0 rows affected (0.00 sec)
    mysql> FLUSH PRIVILEGES;

## Deploy demo ##

Generate `*.war` files

    $ mvn clean package
    
After this step 3 *.war file were generated:

    $ find . -name *.war
    ./saturn/target/saturn.war
    ./core/target/syncope.war
    ./console/target/syncope-console.war

Copy wars to Tomcat's webapps dir

From deploy machine  (<your-domain>)

    # rm -fr /opt/tomcat/webapps/ROOT/*

From development machine

    $ scp ./core/target/syncope.war ./console/target/syncope-console.war root@<your-domain>:/opt/tomcat/webapps
    $ scp ./saturn/target/saturn.war root@<your-domain>:/opt/tomcat/webapps/ROOT

# How this project was created #

A Syncope project includes (at least) two web applications: the core and the console. This page helps you get both web applications up and running with your own project as quickly as possible.


The preferred way to create a Syncope project is to generate a Maven project starting from published archetype.

Hence you need:

* Java SE Development Kit 6 (version 1.6.0-23 or higher) installed;
* Apache Maven (version 3.0.3 or higher) installed;
* Some basic knowledge about Maven;
* Some basic knowledge about Maven WAR overlays;
* Some basic knowledge about Maven archetypes.
 

Maven archetypes are templates of projects. Maven can generate a new project from such a template. For a project using Syncope, you need the website archetype. In the folder in which the new project folder should be created, type the command shown below. On Windows, run the command on a single line and leave out the line continuation characters ('\').


    $ mvn archetype:generate -DarchetypeGroupId=org.apache.syncope -DarchetypeArtifactId=syncope-archetype -DarchetypeRepository=http://repo1.maven.org/maven2 -DarchetypeVersion=1.1.1
    
    [INFO] Scanning for projects...
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] ------------------------------------------------------------------------
    [INFO] 
    [INFO] >>> maven-archetype-plugin:2.2:generate (default-cli) @ standalone-pom >>>
    [INFO] 
    [INFO] <<< maven-archetype-plugin:2.2:generate (default-cli) @ standalone-pom <<<
    [INFO] 
    [INFO] --- maven-archetype-plugin:2.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Interactive mode
    [INFO] Archetype defined by properties
    Downloading: http://repo1.maven.org/maven2/org/apache/syncope/syncope-archetype/1.1.1/syncope-archetype-1.1.1.jar
    Downloaded: http://repo1.maven.org/maven2/org/apache/syncope/syncope-archetype/1.1.1/syncope-archetype-1.1.1.jar (73 KB at 213.7 KB/sec)
    Downloading: http://repo1.maven.org/maven2/org/apache/syncope/syncope-archetype/1.1.1/syncope-archetype-1.1.1.pom
    Downloaded: http://repo1.maven.org/maven2/org/apache/syncope/syncope-archetype/1.1.1/syncope-archetype-1.1.1.pom (7 KB at 49.6 KB/sec)
    Define value for property 'groupId': : org.wizant.syncope.demo
    Define value for property 'artifactId': : demo
    Define value for property 'version':  1.0-SNAPSHOT: : 1.1-SNAPSHOT
    Define value for property 'package':  org.wizant.syncope.demo: : 
    Define value for property 'secretKey': : c6V5wexSYRGjGX2R
     Y: : 
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: syncope-archetype:1.1.1
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: org.wizant.syncope.demo
    [INFO] Parameter: artifactId, Value: demo
    [INFO] Parameter: version, Value: 1.1-SNAPSHOT
    [INFO] Parameter: package, Value: org.wizant.syncope.demo
    [INFO] Parameter: packageInPathFormat, Value: org/wizant/syncope/demo
    [INFO] Parameter: secretKey, Value: c6V5wexSYRGjGX2R
    [INFO] Parameter: package, Value: org.wizant.syncope.demo
    [INFO] Parameter: version, Value: 1.1-SNAPSHOT
    [INFO] Parameter: groupId, Value: org.wizant.syncope.demo
    [INFO] Parameter: artifactId, Value: demo
    [INFO] Parent element not overwritten in /home/alex/projects/syncope/demo1/demo/core/pom.xml
    [INFO] Parent element not overwritten in /home/alex/projects/syncope/demo1/demo/console/pom.xml
    [INFO] project created from Archetype in dir: /home/alex/projects/syncope/demo1/demo
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 3:45.149s
    [INFO] Finished at: Fri May 31 12:09:16 EEST 2013
    [INFO] Final Memory: 14M/239M
    [INFO] ------------------------------------------------------------------------
    
You will be asked for:

1. the groupId (something like `com.mycompany`)
1. the artifactId (something like `myproject`)
1. the version number. You can use the default; it is good practice to have `SNAPSHOT` in the version number during development and the maven release plugin makes use of that string. But ensure to comply to the desired numbering scheme for your project.
1. the package name. The java package name. A folder structure according to this name will be generated automatically; by default, equal to the groupId. 
1. (for archetypeVersion >= 1.0.5) the secretKey. Provide any pseudo-random, 16 character length, string here that will be used in the generated project for AES ciphering.

Maven will create a project for you (in a newly created directory named after the value of the artifactId property you specified) containing two subprojects:

* core - a pre-configured RESTful server, with JPA persistence
* console - a web interface for dealing with the core
* 


