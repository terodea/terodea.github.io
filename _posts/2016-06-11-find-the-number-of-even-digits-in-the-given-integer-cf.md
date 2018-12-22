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
## Step 7 --> Derby Metastore Directory Creation
We need to create a directory named data in $DERBY_HOME directory to store Metastore data.
```shell
$ sudo mkdir $DERBY_HOME/data
```
# Configure Derby Metastore

### STEP 3 --> set hive_home in .bashrc
## STEP 1 --> COPY and RENAME "hive-default.xml.template" as "hive-site.xml"




### STEP 4 --> /apache-hive-2.3.3-bin/bin ./hive



***ERROR*** ---> 

1. Exception in thread "main" java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D


***Add ----> hive-site.xml***<br>
```markdown
<property>
	<name>system:java.io.tmpdir</name>
    <value>/tmp/hive/java</value>
</property>
<property>
	<name>system:user.name</name>
    <value>${user.name}</value>
</property>
```
2. 
	```Shell
	rm -rf $HIVE_HOME/bin/metastore_db
	$HIVE_HOME/bin/schematool -initSchema -dbType derbys
	```
