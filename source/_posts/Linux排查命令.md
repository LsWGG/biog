---
title: Linux排查命令
date: 2021-02-23 11:28:31
tags: Linux
top: 0
---

# ES相关

_cat命令是系统信息查询相关的，下面分成好几个子命令分类。
curl -XGET http://127.0.0.1:9200/_cat

1. 查看ES节点是否正常：curl http://127.0.0.1:9200
2. 查看集群健康状态：curl http://127.0.0.1:9200/_cat/health?v
3. 查看全部索引：curl http://127.0.0.1:9200/_cat/indices?v
4. 创建索引：curl -XPUT http://127.0.0.1:9200/my_new_index
5. 删除索引：curl -XDELETE localhost:9200/example1?pretty
5. 插入数据：curl -XPUT http://127.0.0.1:9200/my_new_index/user/1?pretty -d  '{"name":"张三","age":"23"}'

<!--more-->

# Kafka相关

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

   `bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092  --group test-consumer-group --describe`

   - 查看组名为test-consumer-group的消费组的情况。

## 生产者

1. 发送消息

   `bin/kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic test`
   

# 网络相关

## SSH
1. 查看ssh服务是否正常开启：systemctl status sshd
2. 重启ssh服务：systemctl status sshd
3. 查看ssh开放端口：netstat -tnlp |grep ssh

## 网卡
1. 重启网卡：systemctl restart network
2. 查看本机网关：route -n
3. 查看DNS：cat /etc/resolv.conf

## 进程、端口
1. 对外开放tcp端口：iptables -I INPUT -p tcp --dport port -j ACCEPT
2. 查看防火墙开放的端口：firewall-cmd --zone=public --list-ports
3. 关闭防火墙：systemctl stop firewalld.service
4. 查看防火墙状态： firewall-cmd --state
5. 查看监听的端口：netstat -lnpt

# 硬件相关

1. 查看物理CPU个数

    `cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l`

2. 查看硬盘：df -h
3. 查看内存：free -g

# Python相关

1. 编译pyc：
2. 快速启动web服务：python -m SimpleHTTPServer

# 安全日志

1. 成功登录用户：/var/log/wtmp
2. 尝试登录信息：/var/log/btmp
3. 显示最近登录信息：/var/log/lastlog