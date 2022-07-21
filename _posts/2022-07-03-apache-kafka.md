---
layout: post
title:  "Apache Kafka Windows Setup"
date:   2022-07-03 00:48:59 +0530
categories: Apacha Kafka Event Driven
author: Abhishek Bagati
---

Download kafka: 

<a href="https://kafka.apache.org/downloads">Download</a>

Extract to `C:\kafka`

Find `.bat` files here : `C:\kafka\bin\windows`

change these properties of config files found here : `C:\kafka\config`

in zookeeper.properties :
1. `dataDir= C:/kafka/zookeeper` (any path)
2. `maxClientCnxns=1` (required number)

in server.properties
1. `log.dirs= C:/kafka/kafka-logs` (any path)

ERROR Failed to rename `[C:\kafka/logs/controller.log]`
<a href=https://stackoverflow.com/questions/68466796/issue-with-log4j-1-2-17-version-while-renaming-kafka-log-files-on-windows>solution</a>

## start zookeeper:

`.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties`

## start kafka server :

`.\bin\windows\kafka-server-start.bat .\config\server.properties`

## create topics :

```
.\bin\windows\kafka-topics.bat --create --topic test --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
```

>> Created topic test.


Newer versions(2.2+) of Kafka no longer requires ZooKeeper connection string

`--zookeeper localhost:2181` 

It throws the following exception while creating a topic

> Exception in thread "main" joptsimple.UnrecognizedOptionException:
> zookeeper is not a recognized option

Instead, add Kafka Broker `--bootstrap-server localhost:9092` connection string.


<a href="https://stackoverflow.com/questions/69297020/exception-in-thread-main-joptsimple-unrecognizedoptionexception-zookeeper-is">source</a>



## List topics :

```
.\bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --list
```

>> test


## Describe a topic :

```
.\bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --describe --topic test 
```

>> Topic: test     TopicId: d39VeeFqQLe4YvRjum6g8g PartitionCount: 1       ReplicationFactor: 1    Configs: segment.bytes=1073741824
        Topic: test     Partition: 0    Leader: 0       Replicas: 0     Isr: 0

## Deleting a topic

```
.\bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --delete --topic test1 
```

This is where it all stopped working. Issue being with windows support maybe.
<a href="https://issues.apache.org/jira/browse/KAFKA-10419">KAFKA BROKER Shuts down when a topic is deleted manually from command line on Windows </a>


## starting a consumer

```
bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --consumer.config config/consumer.properties --from-beginning
```

## starting a producer

```
bin\windows\kafka-console-producer.bat --bootstrap-server localhost:9092 --topic test --producer.config config/producer.properties
```

## SSL certificates configuration

```

```

### consumer-3.properties

```
bootstrap.servers=kafka-node-1.lime.ai:9092,kafka-node-2.lime.ai:9092,kafka-node-3.lime.ai:9092
# group.id=test-consumer-group
security.protocol=SSL
ssl.protocol=TLSv1.2
ssl.truststore.location=/datadisk/applications/kafka/ssl/kafka.consumer-3.truststore.jks
ssl.truststore.password=lime123
ssl.keystore.location=/datadisk/applications/kafka/ssl/kafka.consumer-3.keystore.jks
ssl.keystore.password=lime123
ssl.key.password=lime123

```

### producer-3.properties

```
bootstrap.servers=kafka-node-1.lime.ai:9092,kafka-node-2.lime.ai:9092,kafka-node-3.lime.ai:9092
security.protocol=SSL
ssl.protocol=TLSv1.2
ssl.truststore.location=/datadisk/applications/kafka/ssl/kafka.producer-3.truststore.jks
ssl.truststore.password=lime123
ssl.keystore.location=/datadisk/applications/kafka/ssl/kafka.producer-3.keystore.jks
ssl.keystore.password=lime123
ssl.key.password=lime123

```
## start consumer

```
bin\windows\kafka-console-consumer.bat --bootstrap-server=kafka-node-1.lime.ai:9094,kafka-node-2.lime.ai:9094,kafka-node-3.lime.ai:9094 --topic ss-test --consumer.config config/consumer-3.properties --from-beginning
```
```
bin/kafka-console-consumer.sh --bootstrap-server=kafka-node-1.lime.ai:9094,kafka-node-2.lime.ai:9094,kafka-node-3.lime.ai:9094 --topic ss-test --consumer.config config/consumer-3.properties --from-beginning
```

## start producer

```
bin\windows\kafka-console-producer.bat --bootstrap-server=kafka-node-1.lime.ai:9094,kafka-node-2.lime.ai:9094,kafka-node-3.lime.ai:9094 --topic ss-test --producer.config config/producer-3.properties
```
```
bin/kafka-console-producer.sh --bootstrap-server=kafka-node-1.lime.ai:9094,kafka-node-2.lime.ai:9094,kafka-node-3.lime.ai:9094 --topic ss-test --producer.config config/producer-3.properties
```

