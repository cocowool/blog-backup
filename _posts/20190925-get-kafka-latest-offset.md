---
title: 获取Kafka每个分区最新Offset的几种方法
date: 2019-09-25 20:19:25
tag: 
keywords: kafaka, kafka offset, kafka linux
description: 本文介绍如何获取Kafka每个分区Offset的方法。
---

目录

* [脚本方法](#脚本方法)
* [Java 程序](#java-程序)
* [参考资料](#参考资料)



## 脚本方法
```bash
./bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092 --topic test
test:0:1522
test:1:1020
test:2:1258
```
和Java程序比起来，运行得有点慢。

## Java 程序
更详细的代码工程，可以参考我的GitHub[消费者获取分区列表，并获取分区最新的OFFSET](https://github.com/cocowool/sh-valley/tree/master/java/java-kafka)
```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;
import java.util.List;
import java.util.Properties;

import org.apache.kafka.clients.consumer.Consumer;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.PartitionInfo;
import org.apache.kafka.common.TopicPartition;
import org.apache.kafka.common.serialization.LongDeserializer;
import org.apache.kafka.common.serialization.StringDeserializer;

// import kafka.api.OffsetRequest;

public class KafkaConsumerDemo {
    private final static String TOPIC = "test";
    private final static String BOOTSTRAP_SERVERS = "localhost:9092";

    private static Consumer<Long, String> createConsumer() {
        final Properties props = new Properties();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "KafkaExampleConsumer");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, LongDeserializer.class.getName());
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

        final Consumer<Long, String> consumer = new KafkaConsumer<>(props);

        return consumer;
    }

    // 获取某个Topic的所有分区以及分区最新的Offset
    public static void getPartitionsForTopic() {
        final Consumer<Long, String> consumer = createConsumer();

        Collection<PartitionInfo> partitionInfos = consumer.partitionsFor(TOPIC);
        System.out.println("Get the partition info as below:");
        List<TopicPartition> tp =new ArrayList<TopicPartition>();
        partitionInfos.forEach(str -> {
            System.out.println("Partition Info:");
            System.out.println(str);

            tp.add(new TopicPartition(TOPIC,str.partition()));
            consumer.assign(tp);
            consumer.seekToEnd(tp);

            System.out.println("Partition " + str.partition() + " 's latest offset is '" + consumer.position(new TopicPartition(TOPIC, str.partition())));
        });
    }

    // 持续不断的消费数据
    public static void run() throws InterruptedException {
        final Consumer<Long, String> consumer = createConsumer();
        consumer.subscribe(Collections.singletonList(TOPIC));
        final int giveUp = 100; int noRecordsCount = 0;

        while(true){
            final ConsumerRecords<Long, String> consumerRecords = consumer.poll(1000);

            if(consumerRecords.count()==0){
                noRecordsCount++;
                if(noRecordsCount > giveUp) break;
                else continue;
            }

            // int i = 0;
            consumerRecords.forEach(record -> {
                // i = i + 1;
                System.out.printf("Consumer Record:(%d, %s, %d, %d)\n",
                        record.key(), record.value(),
                        record.partition(), record.offset());
            });

            // System.out.println("Consumer Records " + i);
            consumer.commitAsync();
        }

        consumer.close();
        System.out.println("Kafka Consumer Exited");
    }    
}
```

## 参考资料

1. [How can I get the LATEST offset of a kafka topic?](https://stackoverflow.com/questions/38428196/how-can-i-get-the-latest-offset-of-a-kafka-topic)
2. [Kafka 源码解析之 Consumer Poll 模型（七）](https://matt33.com/2017/11/11/consumer-pollonce/)
3. [Case Study to Understand Kafka Consumer and Its Offsets](https://dzone.com/articles/case-study-to-understand-kafka-consumer-and-its-of)












