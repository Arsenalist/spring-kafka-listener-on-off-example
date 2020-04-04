# Starting and Stopping a Kafka Listener after Spring Boot Startup

This example is a fork of the [official example](https://github.com/spring-projects/spring-kafka/tree/master/samples/sample-01).

Start Zookeeper and Kafka from the Kafka directory:

```
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
.\bin\windows\kafka-server-start.bat .\config\server.properties
```

Two topics need to be present:

1. `topic1` - expects JSON with a key of `foo`, so `{"foo": "bar"}`
2. `topic1.DLST` - expects a string like "foo"  - is listening on startup

```
.\bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic topic1
.\bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic topic1.DLT
```

Run the Spring boot application which starts on `localhost:8080`.

- `topic1` listener has not started as in `@KafkaListener(autoStartup="false")`
- `topic1.DLT` listener has not started as `@KafkaListener()`'s `autoStartup` value is `"true"` by default

Create a producer from the command line and send a few JSON object to `topic1` (which is currently off) and send some messages:

```
PS C:\Users\zarar\Tools\kafka_2.13-2.4.1> .\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic topic1
>{"foo": "bar1"}
>{"foo": "bar2"}
>{"foo": "bar3"}
```

Now start the `@KafkaListener` for `topic1` by visiting/CURLing:

http://localhost:8080/start

In the Spring Boot terminal you'll see:

```
2020-04-04 02:49:54.972  INFO 19072 --- [ fooGroup-0-C-1] com.example.Application                  : Received: Foo2 [foo=bar1]
2020-04-04 02:49:54.972  INFO 19072 --- [ fooGroup-0-C-1] com.example.Application                  : Received: Foo2 [foo=bar2]
2020-04-04 02:49:54.972  INFO 19072 --- [ fooGroup-0-C-1] com.example.Application                  : Received: Foo2 [foo=bar3]
```

To stop listening:

http://localhost:8080/stop
