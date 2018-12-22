---
layout: post
title:  "HIVE Installation In Ubuntu"
date:   2016-06-11 03:39:03 +0700
categories: [bigdata, setup]
---
# Installation

## Step 1 --> Download HIVE, Extract and Move it to installation directory.
```shell
wget http://www-us.apache.org/dist/hive/hive-2.1.0/apache-hive-2.1.0-bin.tar.gz
sudo tar xvzf apache-hive-2.1.0-bin.tar.gz -C /usr/local
```
## Step 2 --> Configuring ~/.bashrc
* Open ~/.bashrc and set the environment variable HIVE_HOME to point to the installation directory and PATH:

```shell
export HIVE_HOME=/usr/local/apache-hive-2.1.0-bin
export HIVE_CONF_DIR=/usr/local/apache-hive-2.1.0-bin/conf
export PATH=$HIVE_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:/usr/local/hadoop/lib/*:.
export CLASSPATH=$CLASSPATH:/usr/local/apache-hive-2.1.0-bin/lib/*:.
```
* Activate the new setting for Hive
```shell
source ~/.bashrc
```
## Step 3 --> Creating HIVE warehouse directory
* The directory warehouse is the location to store the table or data related to hive, and the temporary directory tmp is the temporary location to store the intermediate result of processing.
```shell
hdfs dfs -mkdir /user/hive/warehouse
hdfs dfs -chmod g+w /tmp
hdfs dfs -chmod g+w /user/hive/warehouse
```
# STEPS to configure HIVE
To configure Hive with Hadoop, we need to edit the hive-env.sh file, which is placed in the $HIVE_HOME/conf directory. The following commands redirect to Hive conf folder and copy the template file:

## STEP 4 --> COPY AND RENAME "hive-env.sh.template" ad "hive-env.sh"
```shell
$ cd $HIVE_HOME/conf
$ sudo cp hive-env.sh.template hive-env.sh
```
* Edit the hive-env.sh file by appending the following line:
```shell
export HADOOP_HOME=/usr/local/hadoop
```
Hive installation is completed successfully. Now we need an external database server to configure `Metastore`. We use `Apache Derby` database.
# STEPS to configure Apache Derby

## STEP 5 --> Download, Extract, Copy Apache Derby
```shell
$ cd /tmp
$ wget http://archive.apache.org/dist/db/derby/db-derby-10.13.1.1/db-derby-10.13.1.1-bin.tar.gz
$ sudo tar xvzf db-derby-10.13.1.1-bin.tar.gz -C /usr/local
```
## Step 6 --> Derby Environment Variable Setup in ~/.bashrc.
Let's set up the Derby environment by appending the following lines to ~/.bashrc file:
```shell
export DERBY_HOME=/usr/local/db-derby-10.13.1.1-bin
export PATH=$PATH:$DERBY_HOME/bin
export CLASSPATH=$CLASSPATH:$DERBY_HOME/lib/derby.jar:$DERBY_HOME/lib/derbytools.jar
```
## Step 7 --> Derby data Directory Creation
We need to create a directory named data in $DERBY_HOME directory to store Metastore data.
```shell
$ sudo mkdir $DERBY_HOME/data
```
# Configure Hive Metastore
## Step 8 --> Configuring hive-site.xml
* Configuring Metastore means specifying to Hive where the database is stored. We want to do this by editing the `hive-site.xml` file, which is in the `$HIVE_HOME/conf` directory.
```shell
$ cd $HIVE_HOME/conf
$ sudo cp hive-default.xml.template hive-site.xml
```
* Make sure the following lines are between the `<configuration>` and `</configuration>` tags of `hive-site.xml`:
```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
    <description>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    </description>
  </property>
```
## Step 9 --> Configuring `jpox.properties` file.
```
javax.jdo.PersistenceManagerFactoryClass =
org.jpox.PersistenceManagerFactoryImpl
org.jpox.autoCreateSchema = false
org.jpox.validateTables = false
org.jpox.validateColumns = false
org.jpox.validateConstraints = false
org.jpox.storeManagerType = rdbms
org.jpox.autoCreateSchema = true
org.jpox.autoStartMechanismMode = checked
org.jpox.transactionIsolation = read_committed
javax.jdo.option.DetachAllOnCommit = true
javax.jdo.option.NontransactionalRead = true
javax.jdo.option.ConnectionDriverName = org.apache.derby.jdbc.ClientDriver
javax.jdo.option.ConnectionURL = jdbc:derby://hadoop1:1527/metastore_db;create = true
javax.jdo.option.ConnectionUserName = APP
javax.jdo.option.ConnectionPassword = mine
```
**NOTE** : Give full permissions to hive folder.

## Step 10 --> Metastore schema initialization
```shell
$ cd $HIVE_HOME/bin
$ schematool -dbType derby -initSchema
```
## Step 11 --> Let's start Hive
Now that we fixed the errors, let's start Hive CLI:
```shell
$ hive
```
We can exit from that Hive shell by using `exit` command.
**NOTE** :--> One can experience various errors after running `$hive` CLI. Try troubleshooting wiht following options.
***ERROR*** ---> 

1. Exception in thread "main" java.lang.RuntimeException: Couldn't create directory ${system:java.io.tmpdir}/${hive.session.id}_resources
* Fix #1: edit hive-site.xml:
```xml
<property>
    <name>hive.downloaded.resources.dir</name>
    <!--
    <value>${system:java.io.tmpdir}/${hive.session.id}_resources</value>
    -->
    <value>/home/hduser/hive/tmp/${hive.session.id}_resources</value>
    <description>Temporary local directory for added resources in the remote file system.</description>
</property>
```
2. java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D <br>
Replace ${system:java.io.tmpdir}/${system:user.name} by /tmp/mydir in hive-site.xml 
```xml
<property>
    <name>hive.exec.local.scratchdir</name>
    <!--
    <value>${system:java.io.tmpdir}/${system:user.name}</value>
    -->
    <value>/tmp/mydir</value>
    <description>Local scratch space for Hive jobs</description>
  </property>
```