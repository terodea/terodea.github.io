---
layout: post
title:  "Apache PIG Installation In Ubuntu"
date:   2016-12-29 18:34:10 +0700
categories: [bigdata, setup]
---

This `Pig` tutorial briefs how to `install` and `configure` Apache Pig. Apache Pig is an abstraction over MapReduce. Pig is basically a tool to easily perform analysis of larger sets of data by representing them as data flows. 

So, let’s start Pig Installation on Ubuntu.

**Step 1 :-** ***Pre-Requisite to Apache Pig*** <br>
* JAVA : Install `java`,and export path variables in `.bashrc`.
* Hadoop : Install hadoop in pseudo-distribuited mode with YARN as resource manager.

**Step 2 :-** ***Download PIG*** <br>
* You can download latest version of Pig from here [Download](https://pig.apache.org/releases.html).
* Click `Download a release now.`

**Step 3 :-** ***Extract Apache PIG*** <br>
```shell
tar xvf pig-[version].tar.gz
```
**Step 4 :-** ***Move to your desired location. Preferably Hadoop Home***
```shell
cd pig-[version]
sudo mv pig-[version]/* /$HADOOP_HOME
```
**Step 5 :-** ***Adding envrionment variables.***
```shell
export PATH=$PATH:/home/noobwithskills/HADOOP_HOME/pig-[version]]/bin
export PIG_HOME=/home/noobwithskills/HADOOP_HOME/pig-[version]]/
export PIG_CLASSPATH=$HADOOP_HOME/conf
```
**Step 6 :-** ***Update bashrc.***
```shell
source ~/.bashrc
```
**Step 7 :-** ***Check for pig-installation***
```shell
pig -version
```
**NOTE :** If you don't see a pig-version, try troubleshooting or comment down below .

**Step 8 :-** ***Starting Apache Pig***
* In Local Mode : Pig can acces your local filesystem files only.
```shell
pig -x local
```
* In Cluster Mode : Pig can acces HDFS files.
```shell
pig
```
* If all goes well you should see `grunt > ` on your CLI.

So, finally, we have seen how to install Apache Pig on Ubuntu. We will learn Pig Programming in our further Pig tutorials. Feel free to ask your queries in the comment section.