## 安装配置

## 基本架构

## 常用命令
#### 查看topic
```bash
$ kafka-topics.sh --bootstrap-server 192.168.74.130:9092 --list  

$ kafka-topics.sh --bootstrap-server 192.168.74.130:9092 --describe --topic first  
```

#### 创建topic

```bash
# --replication-factor  定义副本数  
# --partitions  定义分区数
$ kafka-topics.sh --bootstrap-server 192.168.74.130:9092 --create --partitions 1 --replication-factor 3 --topic first  

```

#### 修改topic
```bash
$ kafka-topics.sh --bootstrap-server 192.168.74.130:9092 --alter --topic first --partitions 3
```


```bash
$ kafka-topics.sh --bootstrap-server 192.168.74.130:9092 --delete--topic first
```

#### 生产者
```bash
kafka-console-producer.sh --bootstrap-server 192.168.74.130:9092 --topic flink-cdc-kafka-01  
```

#### 消费者
```bash
$ kafka-console-consumer.sh --bootstrap-server 192.168.74.130:9092 --topic flink-cdc-kafka-01  

$ kafka-console-consumer.sh --bootstrap-server 192.168.74.130:9092 --from-beginning --topic flink-cdc-kafka-01
```
