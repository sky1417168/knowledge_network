## 基础知识
#### 基本架构
![[Flume 架构.png]]
**Agent**
Agent是一个JVM进程，它以事件的形式将数据从源头送至目的
Agent主要有3个部分组成，Source、Channel、Sink
- Source
- Channel
	Channel是线程安全的，可以同时处理几个Source的写入操作和几个Sink的读取操作
	Flume自带两种Channel：Memory Channel和File Channel
- Sink
**Event**
Flume数据传输的基本单元，以Event的形式将数据从源头送至目的地
Event由Header和Body两部分组成，Header用来存放该event的一些属性，为K-V结构，Body用来存放该条数据，形式为字节数组

#### 安装部署
```bash
$ tar -zxvf /opt/software/apache-flume-1.9.0-bin.tar.gz-C /opt/module/
$ mv /opt/module/apache-flume-1.9.0-bin/opt/module/flume
$ mv /opt/module/apache-flume-1.9.0-bin/opt/module/flume
```

## 任务场景
#### 监听端口
配置文件flume-netcat-logger.conf
```properties
# Name the components on this agent
# r1 表示 a1 的 Source 的名称
# k1 表示 a1 的 Sink 的名称
# c1 表示 a1 的 Channel 的名称
a1.sources = r1 
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
# capacity 表示总容量1000个 Event
# transactionCapacity 表示手机100条 Event 再提交事务
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
# r1 和 c1 连接
# k1 和 c1 连接
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

启动Flume监控
```bash
$ bin/flume-ng agent --conf conf/ --name a1 --conf-file job/flume-netcat-logger.conf -Dflume.root.logger=INFO,console
```
--conf/-c：表示配置文件存储在conf/目录
--name/-n：表示给agent起名为a1
--conf-file/-f：flume本次启动读取的配置文件是在job文件夹下的flume-telnet.conf文件。
-Dflume.root.logger=INFO,console ：-D表示flume运行时动态修改flume.root.logger参数属性值，并将控制台日志打印级别设置为INFO级别。日志级别包括:log、info、warn、error。

#### 实时监控单个追加文件
![[实时监控单个追加文件.png]]
配置文件flume-file-hdfs.conf
```properties
# Name the components on this agent
a2.sources = r2
a2.sinks = k2
a2.channels = c2

# Describe/configure the source
# exec即execute执行的意思。表示执行Linux命令来读取文件。
a2.sources.r2.type = exec
a2.sources.r2.command = tail -F /opt/module/hive/logs/hive.log
a2.sources.r2.shell = /bin/bash -c

# Describe the sink
a2.sinks.k2.type = hdfs
a2.sinks.k2.hdfs.path = hdfs://hadoop102:9820/flume/%Y%m%d/%H

#上传文件的前缀
a2.sinks.k2.hdfs.filePrefix = logs-

#是否按照时间滚动文件夹
a2.sinks.k2.hdfs.round = true

#多少时间单位创建一个新的文件夹
a2.sinks.k2.hdfs.roundValue = 1

#重新定义时间单位
a2.sinks.k2.hdfs.roundUnit = hour

#是否使用本地时间戳
a2.sinks.k2.hdfs.useLocalTimeStamp = true

#积攒多少个Event才flush到HDFS一次
a2.sinks.k2.hdfs.batchSize = 100

#设置文件类型，可支持压缩
a2.sinks.k2.hdfs.fileType = DataStream

#多久生成一个新的文件
a2.sinks.k2.hdfs.rollInterval = 60

#设置每个文件的滚动大小
a2.sinks.k2.hdfs.rollSize = 134217700

#文件的滚动与Event数量无关
a2.sinks.k2.hdfs.rollCount = 0

# Use a channel which buffersevents in memory
a2.channels.c2.type = memory
a2.channels.c2.capacity = 1000
a2.channels.c2.transactionCapacity = 100

# Bind the source and sink to the channel
a2.sources.r2.channels = c2
a2.sinks.k2.channel = c2
```

启动Flume监控
```bash
$ bin/flume-ng agent --conf conf/ --name a2 --conf-file job/flume-file-hdfs.conf
```

#### 实时监控目录下多个新文件
![[实时监控目录多个文件.png]]
配置文件flume-dir-hdfs.conf
```properties
a3.sources = r3
a3.sinks = k3
a3.channels = c3

# Describe/configure the source
# 定义 source 类型为目录
# spoolDir 监控目录
# fileSuffix 定义文件上传完的后缀
# fileHeader 定义是否有文件头
a3.sources.r3.type = spooldir
a3.sources.r3.spoolDir = /opt/module/flume/upload
a3.sources.r3.fileSuffix = .COMPLETED
a3.sources.r3.fileHeader = true

#忽略所有以.tmp结尾的文件，不上传
a3.sources.r3.ignorePattern = ([^ ]*\.tmp)

# Describe the sink
a3.sinks.k3.type = hdfs
a3.sinks.k3.hdfs.path = hdfs://hadoop102:9820/flume/upload/%Y%m%d/%H

#上传文件的前缀
a3.sinks.k3.hdfs.filePrefix = upload-

#是否按照时间滚动文件夹
a3.sinks.k3.hdfs.round = true

#多少时间单位创建一个新的文件夹
a3.sinks.k3.hdfs.roundValue = 1

#重新定义时间单位
a3.sinks.k3.hdfs.roundUnit = hour

#是否使用本地时间戳
a3.sinks.k3.hdfs.useLocalTimeStamp = true

#积攒多少个Event才flush到HDFS一次
a3.sinks.k3.hdfs.batchSize = 100

#设置文件类型，可支持压缩
a3.sinks.k3.hdfs.fileType = DataStream

#多久生成一个新的文件
a3.sinks.k3.hdfs.rollInterval = 60

#设置每个文件的滚动大小大概是128M
a3.sinks.k3.hdfs.rollSize = 134217700

#文件的滚动与Event数量无关
a3.sinks.k3.hdfs.rollCount = 0

# Use a channel which buffers events in memory
a3.channels.c3.type = memory
a3.channels.c3.capacity = 1000
a3.channels.c3.transactionCapacity = 100

# Bind the source and sink to the channel
a3.sources.r3.channels = c3
a3.sinks.k3.channel = c3
```


启动 Flume 监控
```bash
$ bin/flume-ng agent --conf conf/ --name a3 --conf-file job/flume-dir-hdfs.conf
```

在使用Spooling Directory Source时，不要在监控目录中创建并持续修改文件；上传完成的文件会以.COMPLETED结尾；被监控文件夹每500毫秒扫描一次文件变动。


#### 实时监控目录下多个追加文件
![[实时监控目录下多个追加文件.png]]

配置文件 flume-taildir-hdfs.conf
```properties
a3.sources = r3
a3.sinks = k3
a3.channels = c3

# Describe/configure the source
# 定义 source 的类似为 TAILDIR
# positionFile 指定 positionFile 位置
a3.sources.r3.type = TAILDIR
a3.sources.r3.positionFile = /opt/module/flume/tail_dir.json
a3.sources.r3.filegroups = f1 f2
a3.sources.r3.filegroups.f1 = /opt/module/flume/files/.*file.*
a3.sources.r3.filegroups.f2 = /opt/module/flume/files2/.*log.*

# Describe the sink
a3.sinks.k3.type = hdfs
a3.sinks.k3.hdfs.path = hdfs://hadoop102:9820/flume/upload2/%Y%m%d/%H

#上传文件的前缀
a3.sinks.k3.hdfs.filePrefix = upload-

#是否按照时间滚动文件夹
a3.sinks.k3.hdfs.round = true

#多少时间单位创建一个新的文件夹
a3.sinks.k3.hdfs.roundValue = 1

#重新定义时间单位
a3.sinks.k3.hdfs.roundUnit = hour

#是否使用本地时间戳
a3.sinks.k3.hdfs.useLocalTimeStamp = true

#积攒多少个Event才flush到HDFS一次
a3.sinks.k3.hdfs.batchSize = 100

#设置文件类型，可支持压缩
a3.sinks.k3.hdfs.fileType = DataStream

#多久生成一个新的文件
a3.sinks.k3.hdfs.rollInterval = 60

#设置每个文件的滚动大小大概是128M
a3.sinks.k3.hdfs.rollSize = 134217700

#文件的滚动与Event数量无关
a3.sinks.k3.hdfs.rollCount = 0

# Use a channel which buffers events in memory
a3.channels.c3.type = memory
a3.channels.c3.capacity = 1000
a3.channels.c3.transactionCapacity = 100

# Bind the source and sink to the channel
a3.sources.r3.channels = c3
a3.sinks.k3.channel = c3
```

启动 Flume 监控
```bash
$ bin/flume-ng agent --conf conf/ --name a3 --conf-file job/flume-taildir-hdfs.conf
```

Taildir Source维护了一个json格式的positionFile，其会定期的往positionFile中更新每个文件读取到的最新的位置，因此能够实现断点续传。PositionFile的格式如下：
```json
{"inode":2496272,"pos":12,"file":"/opt/module/flume/files/file1.txt"}{"inode":2496275,"pos":12,"file":"/opt/module/flume/files/file2.txt"}
```

#### 负载均衡
![[负载均衡.png]]

配置文件 flume-netcat-flume.conf
```properties
# Name the components on this agent
a1.sources = r1
a1.channels = c1
a1.sinkgroups = g1
a1.sinks = k1 k2

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 5
a1.sinkgroups.g1.processor.priority.k2 = 10
a1.sinkgroups.g1.processor.maxpenalty = 10000

# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop102
a1.sinks.k1.port = 4141
a1.sinks.k2.type = avro
a1.sinks.k2.hostname = hadoop102
a1.sinks.k2.port = 4142

# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinkgroups.g1.sinks = k1 k2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1
```

配置文件 flume-flume-console1.conf
```properties
# Name the components on this agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1

# Describe/configure the source
a2.sources.r1.type = avro
a2.sources.r1.bind = hadoop102
a2.sources.r1.port = 4141

# Describe the sink
a2.sinks.k1.type = logger

# Describe the channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

配置文件  flume-flume-console2.conf
```properties
# Name the components on this agent
a3.sources = r1
a3.sinks = k1
a3.channels = c2

# Describe/configure the source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop102
a3.sources.r1.port = 4142

# Describe the sink
a3.sinks.k1.type = logger

# Describe the channel
a3.channels.c2.type = memory
a3.channels.c2.capacity = 1000
a3.channels.c2.transactionCapacity = 100

# Bind the source and sinkto the channel
a3.sources.r1.channels = c2
a3.sinks.k1.channel = c2
```

启动 Flume 监控
```bash
$ bin/flume-ng agent --conf conf/ --name a3 --conf-file job/group2/flume-flume-console2.conf -Dflume.root.logger=INFO,console
$ bin/flume-ng agent --conf conf/ --name a2 --conf-file job/group2/flume-flume-console1.conf -Dflume.root.logger=INFO,console
$ bin/flume-ng agent --conf conf/ --name a1 --conf-file job/group2/flume-netcat-flume.conf
```

#### 聚合
![[聚合.png]]

配置文件 flume1-logger-flume.conf
```properties
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/module/group.log
a1.sources.r1.shell = /bin/bash -c

# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop104
a1.sinks.k1.port = 4141

# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

配置文件 flume2-netcat-flume.conf
```properties
# Name the components on this agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1

# Describe/configure the source
a2.sources.r1.type = netcat
a2.sources.r1.bind = hadoop103
a2.sources.r1.port = 44444

# Describe the sink
a2.sinks.k1.type = avro
a2.sinks.k1.hostname = hadoop104
a2.sinks.k1.port = 4141

# Use a channel which buffers events in memory
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

配置文件 flume3-flume-logger.conf
```properties
# Name the components on this agent
a3.sources = r1
a3.sinks = k1
a3.channels = c1

# Describe/configure the source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop104
a3.sources.r1.port = 4141

# Describe the sink

# Describe the sink
a3.sinks.k1.type = logger

# Describe the channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 1000
a3.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a3.sources.r1.channels = c1
a3.sinks.k1.channel = c1
```

启动 Flume 监控
```bash
$ flume]$ bin/flume-ng agent --conf conf/ --name a3 --conf-file job/group3/flume3-flume-logger.conf -Dflume.root.logger=INFO,console
$ bin/flume-ng agent --conf conf/ --name a2 --conf-file job/group3/flume1-logger-flume.conf
$ bin/flume-ng agent --conf conf/ --name a1 --conf-file job/group3/flume2-netcat-flume.conf
```

#### Sink 到 Kafka
配置文件 flume-sink-kafka.conf
```properties
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 监听文件
a1.sources.r2.type = exec
a1.sources.r2.command = tail -F /opt/tmp/tmp.log
a1.sources.r2.shell = /bin/bash -c

# 一行的最大字节数
a1.sources.r1.max-line-length = 1024000

# channels具体配置
a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# KAFKA_sinks
a1.sinks.k1.channel = c1
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.topic = hello
a1.sinks.k1.brokerList = master1:9092,slave1:9092,slave2:9092
a1.sinks.k1.requiredAcks = 1
a1.sinks.k1.batchSize = 20
```

启动 Flume 监控
```bash
$ bin/flume-ng agent --conf conf/ --name a1 --conf-file job/flume-sink-kafka.conf -Dflume.root.logger=DEBUG,console
```
