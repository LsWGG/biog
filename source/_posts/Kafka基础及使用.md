---
title: Kafka基础及使用
date: 2020-11-10 18:02:44
tags: Python
top: 0
---

# Kafka基础及使用

# Kafka命令行操作

## Topic

1. 查看当前服务器中的所有topic

   `bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list`

2. 创建topic

   `bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --create --replication-factor 3 --partitions 1 --topic test`

   - topic 定义topic名
   - replication-factor 定义副本数
   - partitions 定义分区数

3. 删除topic

   `bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --delete --topic test`

4. 查看某个Topic的详情

   `bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --describe --topic test`

## 消费者

1. 消费消息

   `bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --from-beginning --topic test`

   - from-beginning：会把first主题中以往所有的数据都读取出来。根据业务场景选择是否增加该配置。
   - bootstrap-server：生产消息的服务器

2. 查看消费组

   `bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list`

3. 查看某个消费组的详情

   `bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server 127.0.0.1:9092  --group test-consumer-group --describe`

   - 查看组名为test-consumer-group的消费组的情况。

## 生产者

1. 发送消息

   `bin/kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic test`