mac kafka 环境搭建

1.kafka安装

> brew install kafka

2.配置文件位置

> /usr/local/etc/kafka/server.properties
> /usr/local/etc/kafka/zookeeper.properties

3. 启动zookeeper

>./bin/zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &

4.启动 kafka 服务

>./bin/kafka-server-start /usr/local/etc/kafka/server.properties &

5.创建topic

让我们使用单个分区和只有一个副本创建一个名为“test”的主题

> ./bin/kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

6.查看创建的topic

我们现在可以看到该主题，如果我们运行list topic命令：

> ./bin/kafka-topics --list --zookeeper localhost:2181
> test

7.发送一些消息

Kafka提供了一个命令行客户端，它将从文件或标准输入接收输入，并将其作为消息发送到Kafka集群。默认情况下，每行都将作为单独的消息发送。

运行生产者，然后在控制台中键入一些消息发送到服务器。

> .bin/kafka-console-producer --broker-list localhost:9092 --topic test 
> 第一条消息
> 第二条消息

8.消费消息

Kafka还有一个命令行消费者，将消息转储到标准输出。

> ./bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning
> 第一条消息
> 第二条消息

如果你有上面的每个命令运行在不同的终端，那么你现在应该能够输入消息到生产者终端，看到他们出现在消费者终端。

所有命令行工具都有其他选项; 运行没有参数的命令将显示详细记录它们的使用信息。