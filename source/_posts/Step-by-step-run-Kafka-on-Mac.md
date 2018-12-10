---
title: Step by step run Kafka on Mac
date: 2018-12-10 15:45:51
tags:
---

- Download Kafka binary file from _https://kafka.apache.org/downloads_ and install it

- Set up Kafka listen on **PLAINTEXT://localhost:9092**

```bash
terrence@igloo /usr/local/kafka
15:51:25 𝜆 diff config/server.properties config/server.properties.orig
31c31
< listeners=PLAINTEXT://localhost:9092
---
> #listeners=PLAINTEXT://:9092
```

- Start Zookeeper

```bash
igloo:kafka root# bin/zookeeper-server-start.sh config/zookeeper.properties
```

- Start Kafka

```bash
igloo:kafka root# bin/kafka-server-start.sh config/server.properties
```

- Create a new Kafka topic named "test"

```bash
igloo:kafka root# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

- Initialise Producer in Console

```bash
terrence@igloo /usr/local/kafka_2.12-2.1.0
16:04:19 𝜆 bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
>Hello World
>Wow
>
```

- Initialize Consumer in Console to receive the messages

```bash
terrence@igloo /usr/local/kafka_2.12-2.1.0
16:06:11 𝜆 bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
Hello World
Wow
```

Run Kafka Manager, an UI tool for managing Kafka.

```bash
terrence@igloo ~/bin/kafka-manager-1.3.3.18
16:16:47 𝜆 env ZK_HOSTS="localhost:2181" bin/kafka-manager
```

Then visit _http://localhost:9000_

Create a Kafka cluster at first:

![Kafka Cluster](/blog/img/Kafka%20Cluster.png "Kafka Cluster")

Visit "test" topic in Kafka cluster:

![Kafka Manager](/blog/img/Kafka%20Manager.png "Kafka Manager")