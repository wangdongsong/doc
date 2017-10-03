# Kafka集群搭建

标签（空格分隔）： 大数据

---

## 环境配置

### 主机 

IP 192.168.254.200 主机名:wds.master  
IP 192.168.254.201 主机名:wds.slave1  
IP 192.168.254.202 主机名:wds.slave2  

### 目录

> kafka  
> ---- app：应用程序  
> ---- conf：配置文件  
> ---- datalogs：数据文件  
> ---- logs：日志文件  

* app：Kafka从官方下载后，解析上传到该目录，版本：kafka_2.12-0.10.2.0
* conf：存放server.properties、log4j.properties
* datalogs：指定数据文件
* logs：运行的日志文件，由log4j.properties指定

### 配置

### server.properties

增加以下配置项

```
zookeeper.connect=wds.master:2181,wds.slave1:2181,wds.slave2:2181

log.dirs=/home/hadoop/kafka/datalogs

advertised.listeners=PLAINTEXT://192.168.254.200:9092

//集群中唯一，依次为100、200、300
broker.id=100
```


### 运行

```
//根据sh脚本中的命令，设置环境变量，LOG4J的配置指定如下
export KAFKA_LOG4J_OPTS="-Dlog4j.configuration=file:/home/hadoop/kafka/conf/log4j.properties -Dkafka.logs.dir=/home/hadoop/kafka/logs" 

//指定server.properties启动Kafka
./kafka-server-start.sh /home/hadoop/kafka/conf/server.properties

```

### 验证

```
//创建Topic
bin/kafka-topics.sh --create --zookeeper 192.168.254.200:2181 --replication-factor 1 --partitions 1 --topic test

//查看创建的Topic
./kafka-topics.sh --list --zookeeper 192.168.254.200:2181

//创建消费者
bin/kafka-console-consumer.sh --bootstrap-server 192.168.254.200:9092 --topic test --from-beginning

//创建生产者，执行后输入消息
bin/kafka-console-producer.sh --broker-list 192.168.254.201:9092 --topic test
```
