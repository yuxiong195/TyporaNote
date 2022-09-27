# Kafka学习



## Kafka的安装与启动





### 启动命令

**开启zookeeper命令(备注：先进入zookeeper的bin目录)**

```shell
kafka自带zookeeper启动
nohup bin/zookeeper-server-start.sh config/zookeeper.properties >/dev/null 2>&1 & 

官方zookeeper启动
./zkServer.sh start
关闭zookeeper命令(备注：先进入zookeeper的bin目录)
./zkServer.sh stop
```



**Kafka启动命令(备注：先进入kafka目录)**

```shell
常规模式启动kafka

bin/kafka-server-start.sh config/server.properties

进程守护模式启动kafka

nohup bin/kafka-server-start.sh config/server.properties >/dev/null 2>&1 & 
```

以下亦可以，但好像只能用于后台存在zookeeper的情况：

```
bin/kafka-server-start.sh -daemon config/server.properties
```

如果使用kafka自带的zookeeper则需要先后台启动zookeeper：

```
./zookeeper-server-start.sh -daemon ../config/zookeeper.properties
```



**Kafka关闭命令(备注：先进入kafka目录)**

```shell
bin/kafka-server-stop.sh
```

注：可以用jps命令查看是否运行成功（用来查看本地运行着几个java程序，并显示他们的进程号。使用jps时，不需要传递进程号做为参数。）



## kafka基本结构

![image-20220312174319251](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312174319251.png)



### 主题和分区的概念

#### 1. Topic主题

kafka通过topic将不同类型的消息进行分类。消费者也是根据topic进行指定消费

#### 2.Partition分区

通过partition将一个topic中的消息分区存储。这样的好处有多个：

- 分区存储，可以解决统一存储同一文件的容量过大问题
- 可以分布式存储，提高并行读写提高吞吐率



### 副本和leader的概念

示例图：

![image-20220313142850069](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220313142850069.png)

- **副本**：副本是为主题中的分区分别创建多个备份，这些备份一般在每台broker中存有一份（一般情况下我们设置副本与broker是1对1）

- **leader**：kafka会在每个分区的多个副本中选出一个，作为leader。
  - 生产者消费者都是与leader直接通信。
  - leader会与每个follower进行同步

- **isr**：可以同步和已同步的节点会被存入到isr集合中。细节：如果isr中的节点性能较差（如同步效率低，同步失败等）则会被踢出isr集合



### 总结

外链：https://bright-boy.gitee.io/technical-notes/#/kafka/kafka

### 基本配置文件

config

![image-20220310174728525](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220310174728525.png)

server.poperties文件

关注三个配置：

```shell
############################# Server Basics #############################
#The id of the broker. This must be set to a unique integer for each broker.
broker.id=0

############################# Log Basics #############################
# A comma separated list of directories under which to store log files
log.dirs=/tmp/kafka-logs/test-0

############################# Zookeeper #############################
# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
#配置连接 Zookeeper 集群地址（在 zk 根目录下创建/kafka，方便管理）
zookeeper.connect=hadoop102:2181,hadoop103:2181,hadoop104:2181/kafka

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=18000


```



## 基本知识与命令

### Topic相关命令

**kafka-topics.sh**

#### 创建topic

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic test.

#命令选项描述
Command must include exactly one action: --list, --describe, --create, --alter or --delete
Option                                   Description
------                                   -----------
--alter                                  Alter the number of partitions,
                                           replica assignment, and/or
                                           configuration for the topic.
--at-min-isr-partitions                  if set when describing topics, only
                                           show partitions whose isr count is
                                           equal to the configured minimum. Not
                                           supported with the --zookeeper
                                           option.
--bootstrap-server <String: server to    REQUIRED: The Kafka server to connect
  connect to>                              to. In case of providing this, a
                                           direct Zookeeper connection won't be
                                           required.
--command-config <String: command        Property file containing configs to be
  config property file>                    passed to Admin Client. This is used
                                           only with --bootstrap-server option
                                           for describing and altering broker
                                           configs.
--config <String: name=value>            A topic configuration override for the
                                           topic being created or altered.The
                                           following is a list of valid
                                           configurations:
                                                cleanup.policy
                                                compression.type
                                                delete.retention.ms
                                                file.delete.delay.ms
                                                flush.messages
                                                flush.ms
                                                follower.replication.throttled.
                                           replicas
                                                index.interval.bytes
                                                leader.replication.throttled.replicas
                                                max.compaction.lag.ms
                                                max.message.bytes
                                                message.downconversion.enable
                                                message.format.version
                                                message.timestamp.difference.max.ms
                                                message.timestamp.type
                                                min.cleanable.dirty.ratio
                                                min.compaction.lag.ms
                                                min.insync.replicas
                                                preallocate
                                                retention.bytes
                                                retention.ms
                                                segment.bytes
                                                segment.index.bytes
                                                segment.jitter.ms
                                                segment.ms
                                                unclean.leader.election.enable
                                         See the Kafka documentation for full
                                           details on the topic configs.It is
                                           supported only in combination with --
                                           create if --bootstrap-server option
                                           is used.
--create                                 Create a new topic.
--delete                                 Delete a topic
--delete-config <String: name>           A topic configuration override to be
                                           removed for an existing topic (see
                                           the list of configurations under the
                                           --config option). Not supported with
                                           the --bootstrap-server option.
--describe                               List details for the given topics.
--disable-rack-aware                     Disable rack aware replica assignment
--exclude-internal                       exclude internal topics when running
                                           list or describe command. The
                                           internal topics will be listed by
                                           default
--force                                  Suppress console prompts
--help                                   Print usage information.
--if-exists                              if set when altering or deleting or
                                           describing topics, the action will
                                           only execute if the topic exists.
                                           Not supported with the --bootstrap-
                                           server option.
--if-not-exists                          if set when creating topics, the
                                           action will only execute if the
                                           topic does not already exist. Not
                                           supported with the --bootstrap-
                                           server option.
--list                                   List all available topics.
--partitions <Integer: # of partitions>  The number of partitions for the topic
                                           being created or altered (WARNING:
                                           If partitions are increased for a
                                           topic that has a key, the partition
                                           logic or ordering of the messages
                                           will be affected
--replica-assignment <String:            A list of manual partition-to-broker
  broker_id_for_part1_replica1 :           assignments for the topic being
  broker_id_for_part1_replica2 ,           created or altered.
  broker_id_for_part2_replica1 :
  broker_id_for_part2_replica2 , ...>
--replication-factor <Integer:           The replication factor for each
  replication factor>                      partition in the topic being created.
  #备份因子
  
--topic <String: topic>                  The topic to create, alter, describe
                                           or delete. It also accepts a regular
                                           expression, except for --create
                                           option. Put topic name in double
                                           quotes and use the '\' prefix to
                                           escape regular expression symbols; e.
                                           g. "test\.topic".
--topics-with-overrides                  if set when describing topics, only
                                           show topics that have overridden
                                           configs
--unavailable-partitions                 if set when describing topics, only
                                           show partitions whose leader is not
                                           available
--under-min-isr-partitions               if set when describing topics, only
                                           show partitions whose isr count is
                                           less than the configured minimum.
                                           Not supported with the --zookeeper
                                           option.
--under-replicated-partitions            if set when describing topics, only
                                           show under replicated partitions
--version                                Display Kafka version.
--zookeeper <String: hosts>              DEPRECATED, The connection string for
                                           the zookeeper connection in the form
                                           host:port. Multiple hosts can be
                                           given to allow fail-over.

```

**查看zk中所有topic**

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-topics.sh --list --zookeeper localhost:2181
```



#### 创建分区

简单示例：

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 2 --topic test1
Created topic test1.
```

该broker的日志目录下便会生成两个目录：test1-0，test1-1。代表两个分区的数据



#### **创建副本**

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 2 --topic my-replicated-topic
```

代表创建了一个名为my-replicated-topic的topic，它有2个分区，每个分区有3个副本



#### 查看zk中topic的信息

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-topics.sh  --describe --zookeeper localhost:2181 --topic my-replicated-topic
```



#### 外链

[彻底删除Kafka中的topic](https://www.cnblogs.com/musen/p/11475695.html)



### KafKa消息在日志路径中保存的内容

每个broker的log.dirs配置的路径下会存放所有生产者发送的消息

- __consumer_offsets-49_

  kafka内部自己创建了 _consumer_offsets主题，包含了50个分区(可以通过offsets.topic.num.partitions设置，这样可以通过加机器的方式抗大并发）。这个主题用来存放消费组消费某个主题的偏移量。因为每个消费者都会自己维护着消费组的主题偏移量，也就是说每个消费者会把消费的主题的偏移量自主上报给kafka中的某个 _consumer_offsets。

  - 通过如下公式可以选出consumer消费的offset要提交到 _consumer _offsets的哪个分区：hash(consumerGroupld) %_ consumer. offsets主题的分区数
  - 定期将自己消费分区的offset提交给kafka内部topic:_ consumer offsets，提交过去的时候，key是consumerGroupld+topic+分区号，value就是当前offset的值，kafka会定期清理topic里的消息（七天），最后就保留最新的那条数据

- Topic主题及其分区消息
  - ![image-20220312203618046](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312203618046.png)
  - 00000.log：保存的就是消息内容
  - 00000.timeindex





### 生产者发送消息

#### 控制台简单命令

```shell
#示例
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

#命令选项描述
This tool helps to read data from standard input and publish it to Kafka.
Option                                   Description
------                                   -----------
--batch-size <Integer: size>             Number of messages to send in a single
                                           batch if they are not being sent
                                           synchronously. (default: 200)
--broker-list <String: broker-list>      REQUIRED: The broker list string in
                                           the form HOST1:PORT1,HOST2:PORT2.
--compression-codec [String:             The compression codec: either 'none',
  compression-codec]                       'gzip', 'snappy', 'lz4', or 'zstd'.
                                           If specified without value, then it
                                           defaults to 'gzip'
--help                                   Print usage information.
--line-reader <String: reader_class>     The class name of the class to use for
                                           reading lines from standard in. By
                                           default each line is read as a
                                           separate message. (default: kafka.
                                           tools.
                                           ConsoleProducer$LineMessageReader)
--max-block-ms <Long: max block on       The max time that the producer will
  send>                                    block for during a send request
                                           (default: 60000)
--max-memory-bytes <Long: total memory   The total memory used by the producer
  in bytes>                                to buffer records waiting to be sent
                                           to the server. (default: 33554432)
--max-partition-memory-bytes <Long:      The buffer size allocated for a
  memory in bytes per partition>           partition. When records are received
                                           which are smaller than this size the
                                           producer will attempt to
                                           optimistically group them together
                                           until this size is reached.
                                           (default: 16384)
--message-send-max-retries <Integer>     Brokers can fail receiving the message
                                           for multiple reasons, and being
                                           unavailable transiently is just one
                                           of them. This property specifies the
                                           number of retires before the
                                           producer give up and drop this
                                           message. (default: 3)
--metadata-expiry-ms <Long: metadata     The period of time in milliseconds
  expiration interval>                     after which we force a refresh of
                                           metadata even if we haven't seen any
                                           leadership changes. (default: 300000)
--producer-property <String:             A mechanism to pass user-defined
  producer_prop>                           properties in the form key=value to
                                           the producer.
--producer.config <String: config file>  Producer config properties file. Note
                                           that [producer-property] takes
                                           precedence over this config.
--property <String: prop>                A mechanism to pass user-defined
                                           properties in the form key=value to
                                           the message reader. This allows
                                           custom configuration for a user-
                                           defined message reader.
--request-required-acks <String:         The required acks of the producer
  request required acks>                   requests (default: 1)
--request-timeout-ms <Integer: request   The ack timeout of the producer
  timeout ms>                              requests. Value must be non-negative
                                           and non-zero (default: 1500)
--retry-backoff-ms <Integer>             Before each retry, the producer
                                           refreshes the metadata of relevant
                                           topics. Since leader election takes
                                           a bit of time, this property
                                           specifies the amount of time that
                                           the producer waits before refreshing
                                           the metadata. (default: 100)
--socket-buffer-size <Integer: size>     The size of the tcp RECV size.
                                           (default: 102400)
--sync                                   If set message send requests to the
                                           brokers are synchronously, one at a
                                           time as they arrive.
--timeout <Integer: timeout_ms>          If set and the producer is running in
                                           asynchronous mode, this gives the
                                           maximum amount of time a message
                                           will queue awaiting sufficient batch
                                           size. The value is given in ms.
                                           (default: 1000)
--topic <String: topic>                  REQUIRED: The topic id to produce
                                           messages to.
--version                                Display Kafka version.

```



#### 生产者端的同步发送和异步发送

 异步性能高一些，但可能存在消息丢失的情况。所以大部分还是采用同步发送



异步发送逻辑图

![image-20220314093348468](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220314093348468.png)

```
异步发送，生产者发送完消息后就可以执行之后的业务，broker在收到消息后异步调用生产者提供的callback回调方法。
```



#### 生产者客户端相关配置

##### 消息发送的确认机制

**acks(DEFAULT=1)**

这个配置代表生产者要求领导者在完成请求和返回之前如何进行操作。这控制了发送的记录的持久性。允许以下设置：

- `acks=0`如果设置为零，则**生产者根本不会等待服务器的任何确认**。该记录将立即添加到套接字缓冲区并被视为已发送。这种情况下不能保证服务器已经收到记录，`retries`配置不会生效（因为客户端一般不会知道有任何故障）。每条记录返回的偏移量将始终设置为 -1。
- `acks=1`这意味着leader会将记录写入其本地日志，但会在不等待所有追随者的完全确认的情况下做出响应。在这种情况下，如果领导者在确认记录完成后但followers同步消息之前失败，那么消息将丢失。
- `acks=all`这意味着**leader将等待完整的同步副本完成后再发送确认ack**。这保证了只要至少一个同步副本保持活动状态，记录就不会丢失。这是最有力的保证。这相当于 acks=-1 设置。

![image-20220314101703341](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220314101703341.png)

ack接受失败重试等配置

retry.backoff.ms



**java代码**

```java
props.put(ProducerConfig.ACKS_CONFIG,1);// 设置acks参数
```



##### 生产者缓冲区配置

**buffer.memory**

kafka默认会创建一个消息缓冲区，用来存放要发送的消息，缓冲区是32m

```java
props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432);// 默认值32MB
props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);//本地线程向缓冲区一次拉取的数据大小，默认16KB
```

![image-20220314103116234](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220314103116234.png)

### 消费者接收消息

```shell
#示例
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test

#命令选项描述
This tool helps to read data from Kafka topics and outputs it to standard output.
Option                                   Description
------                                   -----------
--bootstrap-server <String: server to    REQUIRED: The server(s) to connect to.
  connect to>
--consumer-property <String:             A mechanism to pass user-defined
  consumer_prop>                           properties in the form key=value to
                                           the consumer.
--consumer.config <String: config file>  Consumer config properties file. Note
                                           that [consumer-property] takes
                                           precedence over this config.
--enable-systest-events                  Log lifecycle events of the consumer
                                           in addition to logging consumed
                                           messages. (This is specific for
                                           system tests.)
--formatter <String: class>              The name of a class to use for
                                           formatting kafka messages for
                                           display. (default: kafka.tools.
                                           DefaultMessageFormatter)
--from-beginning                         If the consumer does not already have
                                           an established offset to consume
                                           from, start with the earliest
                                           message present in the log rather
                                           than the latest message.
--group <String: consumer group id>      The consumer group id of the consumer.
--help                                   Print usage information.
--isolation-level <String>               Set to read_committed in order to
                                           filter out transactional messages
                                           which are not committed. Set to
                                           read_uncommittedto read all
                                           messages. (default: read_uncommitted)
--key-deserializer <String:
  deserializer for key>
--max-messages <Integer: num_messages>   The maximum number of messages to
                                           consume before exiting. If not set,
                                           consumption is continual.
--offset <String: consume offset>        The offset id to consume from (a non-
                                           negative number), or 'earliest'
                                           which means from beginning, or
                                           'latest' which means from end
                                           (default: latest)
--partition <Integer: partition>         The partition to consume from.
                                           Consumption starts from the end of
                                           the partition unless '--offset' is
                                           specified.
--property <String: prop>                The properties to initialize the
                                           message formatter. Default
                                           properties include:
                                                print.timestamp=true|false
                                                print.key=true|false
                                                print.value=true|false
                                                key.separator=<key.separator>
                                                line.separator=<line.separator>
                                                key.deserializer=<key.deserializer>
                                                value.deserializer=<value.
                                           deserializer>
                                         Users can also pass in customized
                                           properties for their formatter; more
                                           specifically, users can pass in
                                           properties keyed with 'key.
                                           deserializer.' and 'value.
                                           deserializer.' prefixes to configure
                                           their deserializers.
--skip-message-on-error                  If there is an error when processing a
                                           message, skip it instead of halt.
--timeout-ms <Integer: timeout_ms>       If specified, exit if no message is
                                           available for consumption for the
                                           specified interval.
--topic <String: topic>                  The topic id to consume on.
--value-deserializer <String:
  deserializer for values>
--version                                Display Kafka version.
--whitelist <String: whitelist>          Regular expression specifying
                                           whitelist of topics to include for
                                           consumption.
```



#### 偏移量与两种方式

偏移量理解

![image-20220312151627980](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312151627980.png)

- 在每个broker的log.dirs配置的路径下会存放所有生产者发送的消息。
- 在该目录下会为每个topic创建一个目录，存放消息的信息等，这个存放文件会被覆盖更新。 
- 消息的保存是有序的，通过偏移量来保证消息的有序性；消费者消费时也是通过偏移量来描述当前消费的起始位置

如下面的test-0就是一个topic， .log文件存放消息：

```sh
[root@hadoop100 test-0]# pwd
/tmp/kafka-logs/test-0
[root@hadoop100 test-0]# ll
总用量 8
-rw-r--r--. 1 root root 10485760 3月  12 14:28 00000000000000000000.index
-rw-r--r--. 1 root root      367 3月  12 14:54 00000000000000000000.log
-rw-r--r--. 1 root root 10485756 3月  12 14:28 00000000000000000000.timeindex
-rw-r--r--. 1 root root        8 3月  12 14:28 leader-epoch-checkpoint

```





方式一：从头开始消费

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic test
```



方式二：偏移量消费

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test
```



#### 消费组与消费方式

**消费组**

即不同消费者归属的同一个组ID下

**查看消费组详细信息**

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group testGroup
```

![image-20220312172236362](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312172236362.png)

重点关注以下几个信息：

- current-offset：该消费组当前消费的消息偏移量

- LOG-END-OFFSET：消息总量（即该topic下的最后一条消息偏移量）

- LAG：积压的消息（即LOG-END-OFFSET - current-offset）

- 当前主题生产者的消息偏移量：8

  ![image-20220312172737232](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312172737232.png)



**单播消息**

即一个消费组中如果有多个消费者订阅了同一份topic，那只能有一个消费者接受到订阅的topic消息

```sh
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-consumer.sh --consumer-property group.id=testGroup --bootstrap-server localhost:9092 --topic test
```



**多播消息**

即多个消费组订阅了同一个topic，每个消费组中的其中一个消费者可以接收到消息

```shell
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-consumer.sh --consumer-property group.id=testGroup --topic test
[root@hadoop100 kafka_2.12-2.3.0]# bin/kafka-console-consumer.sh --consumer-property group.id=testGroup1 --topic test
```

**示例图**

![image-20220312163932353](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220312163932353.png)



#### topic分区情况下的消费方式

kafka集群分区情况下，不能保证topic的总的消费顺序性，只能保证在一个partition的范围内消息消费的局部顺序性。一个消费者可以消费多个partiton。

![image-20220313214919766](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220313214919766.png)



#### 消费者客户端相关配置

##### 自动提交和手动提交（offset）

```java
// 是否自动提交offset，默认就是true
props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "true");
// 自动提交offset的间隔时间
props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");

```

消费者poll到消息后默认情况下，会自动向broker的_consumer_offsets主题提交当前主题-分区消费的偏移量。

自动提交会丢消息： 因为如果消费者还没消费完poll下来的消息就自动提交了偏移量，那么此时消费者挂了，于是下一个消费者会从已提交的offset的下一个位置开始消费消息。之前未被消费的消息就丢失掉了。



手动提交情况则需要在程序中手动提交

```java
//springbooot
@KafkaListener(topics = {TOPIC_NAME},groupId = "MyGroup1")//订阅了test主题
public void handleMessage(ConsumerRecord record, Acknowledgment ack) {

    System.out.println("record: "+record);
    System.out.println("方法处理了："+record.value());

    //手动提交offset
    ack.acknowledge();
}
```





## kafka集群操作

### 集群部署命令

该情况下的虚拟机对外ip[暴露的ip]和真实ip[ifconfig显示的ip]可能只是映射关系，用户访问对外ip时，OpenStack会转发到对应的真实ip实现访问。
但此时如果 Kafka server.properties配置中的listeners=PLAINTEXT://xx.xx.xx.xx:9092中的ip配置为[对外ip]的时候无法启动，因为socket无法绑定监听，报如下错误
解决方法也很简单，listeners=PLAINTEXT://10.20.30.153:9092中的ip改为真实ip[ifconfig中显示的ip]即可，其他使用时正常使用对外ip即可，跟真实ip就没有关系了。
例:假如服务器对外访问的ip为123.44.55.66 内网地址为10.20.30.153

listeners=PLAINTEXT://10.20.30.153:9092
advertised.listeners=PLAINTEXT://123.44.55.66:9092



### 集群消息发送与消费

- 发送消息

  ```shell
  bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic mytest-topic
  ```

- 消费消息命令

  ```shell
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092,localhost:9093,localhost:9094 --topic mytest-topic
  ```

  

### 关闭kafka节点

```shell
ps aux | grep server.properties
kill 进程号
```





## kafka线上问题优化

参考外链：https://bright-boy.gitee.io/technical-notes/#/kafka/kafka?id=%e5%8d%81%e3%80%81kafka%e7%ba%bf%e4%b8%8a%e9%97%ae%e9%a2%98%e4%bc%98%e5%8c%96

### 如何防止消息丢失

- 发送方： ack是 1 或者-1/all 可以防止消息丢失，如果要做到99.9999%，ack设成all，把min.insync.replicas配置成分区备份数
- 消费方：把自动提交改为手动提交。（自动提交消息丢失原因是因为其poll完消息不等消费就提交偏移量，可能会出现消费者挂的情况，导致消息丢失）



### 如何防止消息的重复消费

**上游问题**

生产者关闭生产重试：可能存在网络抖动等情况，造成生产者ack响应失败。默认生产者会认为消息发送失败会重试，这里把重试关闭（不推荐，可能会造成消息丢失）。



**下游问题：保证幂等性**

![image-20220318105944565](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220318105944565.png)





### 如何保证消息消费的顺序性

- 生产者：使用同步发送（防止异步发送丢失消息重传情况，导致顺序混乱），ack值设置为非0
- 消费者：主题只能设置一个分区，然后被一个消费者消费

使用场景不多
