Clypper_HQ_Server
=================

Software requirements
=====================

    - Maven 3.x
    - MySQL 5+
    - JDK 1.7+



Initializing the database
=========================
To initialize a sample database for development, run the following command from the root of the project:
    mvn -pl clypper-db/db-init -am -P db-init -Ddb.mysql-root-password=root clean package

"db.mysql-root-password" should be the password of the MySQL "root" account. Replace "root" with the correct one
before running the above command. Can be empty as well.



Applying database migrations
============================
During development, people will create database migration scripts.
To apply those to your database, run the following command from the root of the project:
    mvn -pl clypper-db/db-init -am clean compile flyway:migrate

The above command assumes that the database already exists (see the step "Initializing the database")



Reverse-engineer the database
=============================
The project is using Hibernate for the data layer.
The approach is to let the database lead (as opposed to the Java code or the Hibernate mappings).

So, whenever the database is changed, you need also to re-generate the hibernate POJOs and mappings.
To do this, run the following command from the root of the project:
    mvn -pl clypper-db/db-model -am -P database-reveng clean package

Run integration tests
=====================
To run integration tests, in the root of the project, run a phase higher than "run-integration-test", like "verify", "install" or "deploy":
    Example: mvn clean verify -P run-integration-test
If you want to prevent integration tests from running, add the "skipIntegrationTests" system property:
    mvn clean install -DskipIntegrationTests


(OPTIONAL) Running Clypper without an IDE and an installed servlet container
==================

Change directory from the project's root folder to 'clypper-web'.

Execute the following command:


  mvn clean verify org.codehaus.cargo:cargo-maven2-plugin:run -Dcargo.maven.containerId=tomcat7x -Dcargo.maven.containerUrl=http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.16/bin/apache-tomcat-7.0.16.zip


Go to: http://localhost:8080/clypper-web/

SETTING UP SSL IN TOMCAT (HTTPS CONFIGURATION)
=====================

###To create Certificate:-

1) Open Terminal and Type : "cd JAVA_HOME" (per-requisite Java-Home path must be set)

2) Type commmand "keytool -genkey -alias clypper -keyalg RSA"

3) When you type above command you will see some questions. 
```
[user]:bin [user] keytool -genkey -alias clypper -keyalg RSA
Enter keystore password:  [password]
Re-enter new password: [password]
What is your first and last name?
  [Unknown]: [Name]
What is the name of your organizational unit?
  [Unknown]: [organizational unit]
What is the name of your organization?
  [Unknown]:  [organization]
What is the name of your City or Locality?
  [Unknown]:  [City]
What is the name of your State or Province?
  [Unknown]:  [State]
What is the two-letter country code for this unit?
  [Unknown]:  [country code]
Is CN=[Name], OU=[organizational unit], O=[organization], L=[City], ST=[State], C=[country code] correct?
  [no]:  yes
 
Enter key password for
    (RETURN if same as keystore password):  [password]
Re-enter new password: [password]
```

4) Now, ".keystore" is created in root directory for user e.g "Users/[user]/.keystore"

###Configuration for Tomacat:-

1) Open Tomcat directory and open server.xml e.g "apache-tomcat-7.0.55/conf/server.xml"

2) Find the below commented line from server.xml and replace with given code in 3rd step
```<!--
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
    maxThreads="150" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS" />
-->
```

3) Change the code with this
```
<Connector SSLEnabled="true" acceptCount="100" clientAuth="false"
     disableUploadTimeout="true" enableLookups="false" maxThreads="25"
     port="8443" keystoreFile="/Users/[user]/.keystore" keystorePass="password"
     protocol="org.apache.coyote.http11.Http11NioProtocol" scheme="https"
     secure="true" sslProtocol="TLS" />
```
4) Make sure to change the location of "keystoreFile" as per your location and set your password in "keystorePass"
