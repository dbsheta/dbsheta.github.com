---
title: "Processing Streaming Twitter Data using Kafka and Spark — Part 1: Setting Up Kafka Cluster"
date: 2018-11-05 00:25:00 +05:30
categories: [kafka, zookeeper, setup]
excerpt_separator: <!--more-->
---

As per the plan I laid out in my previous post, I’ll start by setting up a Kafka Cluster. I’ll primarily be working on Google Cloud instances throughout this series, however, I’ll also lay down steps to setup the same in your local machines as well.
<!--more--> 
Also, in this series, main focus will be on how-to rather than how-does-it. We’ll spend most of time learning how to implement various use cases than how does Kafka/Spark/Zookeeper does it. However, We’ll go into theory mode if there aren’t any sources already available on the web.

<br><br>



## Apache Zookeeper

Kafka uses [Zookeeper](https://zookeeper.apache.org/) to store metadata about the Kafka cluster, as well as consumer client details. There are many articles online which explain why Kafka needs Zookeeper. [This](https://data-flair.training/blogs/zookeeper-in-kafka/) article by Data-Flair does it very well.

While you can get a quick-and-dirty single node  Zookeeper server up and running directly using scripts contained in the Kafka distribution, it is trivial to install a full version of Zookeeper from the distribution.


![Source: Kafka- The Definitive Guide](https://cdn-images-1.medium.com/max/2000/1*0vTDGwBRzFsexzhtiJ4OsQ.png)

I assume you have JDK1.8 installed. If not Linux/macOS users can download openJDK using package managers. Windows users can go to Oracle’s website and install the same.

<br><br>


### **Zookeeper Standalone Mode**

Those who don’t have any cloud resources available like Google Cloud or Azure or AWS, can run a single node standalone Zookeeper instance. Spinning such an instance is fairly simple

Download latest version of [Zookeeper](https://www.apache.org/dyn/closer.cgi/zookeeper/).

```bash
    tar -xvf zookeeper-X.X.X.tar.gz -C /opt
    ln -s /opt/zookeeper-X.X.X /opt/zookeeper
    cd /opt/zookeeper
    cat conf/zoo_sample.cfg >> zookeeper.properties
    bin/zkServer.sh start conf/zookeeper.properties
```

<br><br>


### **Zookeeper Ensemble**

A Zookeeper cluster is called an *ensemble*. Due to the algorithm used, it is recommended that ensembles contain an odd number of servers (3, 5,…) as a majority of ensemble members (a quorum) must be working in order for Zookeeper to respond to requests. It is also *not* *recommended* to run more than seven nodes, as performance can start to degrade due to the nature of the consensus protocol.

To configure a Zookeeper ensemble, all servers must have a common configuration and each server needs a ***myid*** file in the data directory that specifies the ID number of the server

Except the last command, run all previous commands on all servers. In addition to that following are to be run on all servers:

1. Add list of your servers (hostname/IP) to bottom of the zookeeper configuration file:
```properties
    server.1=X.X.X.X:2888:3888
    server.2=Y.Y.Y.Y:2888:3888
    server.3=Z.Z.Z.Z:2888:3888
```

2. Add myid file at dataDir location which in my case is */tmp/zookeeper*:
```bash
    touch /tmp/zookeeper/myid
    echo 1 >> /tmp/zookeeper/myid
```

3. After making the above changes, start zookeeper on all servers one by one. 
```bash
    bin/zkServer.sh start conf/zookeeper.properties
```

<br><br>


## Setting up Kafka

Download latest version of [Kafka](http://mirrors.wuchna.com/apachemirror/kafka/2.0.0/kafka_2.11-2.0.0.tgz) on all your serves.
```bash
    tar -xvf kafka_2.11-0.11.0.0.tgz –C /opt
    ln -s /opt/kafka_2.XX /opt/kafka
```
Update kafka server.properties file in all instances (hostname/IP) to contain the below line. This file is located in */opt/kafka/config/server.properties*
```properties
    zookeeper.connect=X.X.X.X:2181,Y.Y.Y.Y:2181,Z.Z.Z.Z:2181
```

<br>
### **Single Node Multi Broker (SNMB):**

For folks who don’t have cloud instances handy, you can setup a cluster locally. 

1. You have to copy *server.properties* file and copy it 3 times with different name like server1.properties, server2.properties, etc.

1. Every Kafka broker must have an integer identifier. Open configuration file and change *broker.id=1* for 1st server *broker.id=2* for 2nd and so on. A good guideline is to set this value to something intrinsic to the host so that when performing maintenance it is easier to map broker IDs to hosts

1. As you will be running multiple instances on same machine, change port configuration so each process uses different port number. Open configuration file and change *port=9092* on 1st, 9093 on 2nd and so on.

1. Also, in future, whenever I give a command for MNMB setup, you should automatically map it to your configuration

<br>
### **Multi Node Multi Broker (MNMB):**

1. Open configuration file and change *broker.id=1* for 1st server, *broker.id=2* for 2nd and so on.

1. Add the canonical hostnames of your servers in your hosts file if they are not public. Or you’ll need to overwite on each server instance *advertised.listeners=PLAINTEXT://your.host.name:9092*


<br><br>
## Testing Full Setup

1. We have already started a zookeeper ensemble, now lets start Kafka in all our servers as well.
```bash
    cd /opt/kafka
    bin/kafka-server-start.sh config/server.properties
```
2. Let’s create a sample topic with 3 partitions and 2 replicas:
```bash
    bin/kafka-topics.sh --create --zookeeper X.X.X.X:2181 --replication-factor 2 --partitions 3--topic sample_test
```
3. Kafka has a command line consumer that will dump out messages to standard output.
```bash
    bin/kafka-console-consumer.sh — zookeeper X.X.X.X:21812181 — topic sample_test — from-beginning
```
4. Run the producer and then type a few messages into the console to send to the server.
```bash
    bin/kafka-console-producer.sh --broker-list  X.X.X.X:9092 --topic sample_test
    > Hello, World!
    > Hello from the other side.
```
If you see output in the console consumer window, congratulations! You successfully setup zookeeper and kafka cluster locally/ on cloud. If for some reasons you are getting errors or not able to get the desired ouput, please leave a comment.

We will use the same setup in the upcoming few articles. In the next article, we will see how we can implement a Kafka Client which will read latest tweets from Twitter and push them into Kafka.

**Until then,** 

![Goodbye](https://thumbs.gfycat.com/GenuineMenacingImperialeagle-size_restricted.gif)
