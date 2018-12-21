---
layout: post
title:  "MongoDB Setup In LINUX Based OS"
date:   2016-06-11 03:43:45 +0700
categories: [nosql, setup]
---

**STEP 1:-** <br>
Create `db-path` according to MongoDB specification

```shell       
sudo mkdir -p /data/db
```
 
**STEP 2:-** <br>
Give `RWX` Permission

```shell      
sudo chmod -R 777 /data/db
```

**STEP 3:-** <br>
Download MongoDB from the official site; and Extract

```shell
cd Downloads
tar xvzf mongodb-linux-x86_64-ubuntu1604-4.0.4.tgz
```

**STEP 4:-** <br>
Make Installation Path

```shell
sudo mkdir -p /usr/local/mongoDB
```

**STEP 5:-** <br>
Move Extracted Files to Setup Location <br>
`CD` to `extracted folder of MongoDB`
```shell
sudo mv /mongodb-linux-x86_64-ubuntu1604-4.0.4/* /usr/local/mongoDB
```
**STEP 6:-** <br>
ADD mongoDB enivronment variable to `".bashrc"`
```shell
sudo gedit ~/.bashrc
#`Append` at the end of `".bashrc"`
export MongoDB_HOME=/usr/local/mongo
export PATH=$PATH:/usr/local/mongo/bin
```


**STEP 7:-** <br>
START MongoDB server
* `Start` The MongoDB `Server`
    ```
        mongod
    ```
* `Start` MongoDB `Shell`
    ```
        mongo
    ```

You should see mongo CLI running by now.If not you need to troubleshoot and check with the errors.


So, finally, we have seen how to install MongoDB on Ubuntu. We will learn MongoDB Programming in our further MongoDB tutorials. Feel free to ask your queries in the comment section.
