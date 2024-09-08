---
layout: default
title: "Getting Started"
nav_order: 2
---

# Getting Started

## Dependencies

The below dependency is the bare minimum one that will be needed with any project. The remaining depends on the type of the project. The below dependency add the core layer of HMF4J to your project

```xml
<dependency>
    <groupId>io.github.handy-messaging-framework</groupId>
    <artifactId>hmf4j-core</artifactId>
    <version>1.1.0</version>
</dependency>
```

## Quick Example

Example here illustrates how to use the HMF4J framework to send and receive messages from the Apache Kafka messaging system.

### Pre-requisites for the example
- Kafka ver 2.13 running
- Create a topic named `demo_topic` in Kafka

Apart from the dependencies listed above, include the following dependecies to your `pom.xml` file.

```xml
<dependency>
    <groupId>io.github.handy-messaging-framework</groupId>
    <artifactId>hmf4j-kafka-connector</artifactId>
    <version>1.1.0</version>
</dependency>
<dependency>
    <groupId>io.github.handy-messaging-framework</groupId>
    <artifactId>hmf4j-types-simplemessage</artifactId>
    <version>1.1.0</version>
</dependency>
```

The above dependencies add the following capabilities:
- handy-messaging-framework's Kafka connector which enables the framework to interface with Kafka
- SimpleMessage - A messaging schema designed on Google's protobuf standard. More details on this later.

### Configuration file
Create a file by the name `hmf4j-conf.yml` in the application's `resource` directory

The contents of the `hmf4j-conf.yml` file will look as:
```yaml
hmf4j:
 profiles:
  - profileName: profile1
    system: kafka
    consumer:
      properties:
        bootstrap.servers: localhost:9092
        group.id: test_app
        max.messages.per.batch: 3
        max.poll.duration.millis: 10000
    producer:
      properties:
        bootstrap.servers: localhost:9092
```

In the configuration we have defined a profile called `profile1` which uses `kafka` as the messaging system. Its producer and consumer properties are defined below.

Lets take the case of the Producer example now. The producer in a messaging system creates and sends messages through the messaging channel. In our example the producer will be a Kafka producer.

### Sending a message


```java

public class SimpleProducer {
    MessageProducerSystem producerSystem;

    /**
     * Initializes the producer system. Producer is asked to use `profile1` and write to `demo_topic`
     */
    public void SimpleProducer() {
        producerSystem = new MessageProducerSystem("profile1", "demo_topic");
    }

    /**
     * Function sends the message generated using the producer to the Kafka `demo_topic`
     */
    public void sendMsg() {
        SimpleMessage contentMsg = generateMessage();
        this.producerSystem.sendMessage(contentMsg);
        this.producerSystem.close();
        System.out.println("Message sent");
    }

    /**
     * Function generates a message with SimpleMessage schema.
     *
     * @return
     */
    private SimpleMessage generateMessage() {
        SimpleMessage contentMsg = new SimpleMessage();
        contentMsg.setContentSchema(String.class.toString());
        contentMsg.setDateTime(Optional.of(Date.from(Instant.now())));
        contentMsg.setMessageId("msg-1");
        contentMsg.setPayload("Hello, this is a sample message".getBytes());
        contentMsg.setSender("app-1");
        return contentMsg;
    }
}

```

The `producerSystem` uses `profile1` as the profile and writes to `demo_topic`. `profile1` is defined in the `hmf4j-conf.yml` to use the Kafka messaging system. 
Run the program and verify that the data lands in the `demo_topic` in Kafka. Use the protobuf compiler to deserialize and verify the data written to the Kafka topic.

### Consuming message

#### Initialzing the Message Consumer

```java
public class SimpleConsumer {

    MessageConsumingSystem consumingSystem;
    SimpleHandler messageHandler;

    /**
     * Type of the message expected to be received by the consumer.
     * In this case it is of the type SimpleMessage
     */

    String getMessageTypeClass(){
        return "io.github.handy.messaging.types.simplemessage.SimpleMessage";
    }

    /**
     * Initializes and starts the consumer. The consumer is initialized with profile1 and the demo_topic.
     * The consumer will start listening from this point.
     * Once a message is received in the channel, the `messageHandler` is invoked.
     */

    public SimpleConsumer(){
        messageHandler = new SimpleHandler();
        MessageConsumingSystem.getInstance().setupConsumer("profile1",
                "demo_topic",
                getMessageTypeClass(), messageHandler);
    }
}

```

The above class initializes and starts the MessageConsumingSystem. It is asked to use `profile1`. This implies the consumer will connect to Kafka. The kafka topic to which it listens to will be `demo_topic`. 

When a message is received by the consumer it passes it over to the messageHandler defined below:

#### Message Handler

```java
public class SimpleHandler implements MessageHandler {

    /**
     * Function that gets invoked for every message read from the channel
     * @param message - Message received from the channel
     */
    @Override
    public void handleMessage(Message message) {
        SimpleMessage msg = (SimpleMessage) message;
        System.out.println(String.format("Id: %s",msg.getId()));
        System.out.println(String.format("Message payload: %s", new String(msg.getId())));
    }

    /**
     * Function decides if a new instance of the handler is needed for each of the messages received
     * @return optional MessageHandler
     */
    @Override
    public Optional<MessageHandler> getNewInstance() {
        return Optional.empty();
    }
}

```

The `SimpleHandler` class here is an extension of `MessageHandler` which is a pre-requisite for registering a class as a message handler. Whenever a new message is received, the `handleMessage(Message message) function gets invoked`. The `getNewInstance()` function decides if a new instance of the SimpleHandler class is needed for every message received from the input channel.

# Springboot Integration
The framework provides an easy way to integrate with your Springboot application. This is by using the `hmf4j-springboot` artifact. The maven coordinates for this artifact is given below:

```xml
<dependency>
    <groupId>io.github.handy-messaging-framework</groupId>
    <artifactId>hmf4j-springboot</artifactId>
    <version>1.1.0</version>
</dependency>
```

Here is an example of the above code using HMF4J's Springboot Integration.

The configuration file - `hmf4j-conf.yml` remains the same as above.


### Springboot Main Class

```java
@ComponentScan(basePackages = {"io.github.handy.messaging", "org.example"})
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }
}
```

The `main` class has explicit scanning for the package prefix `io.github.handy.messaging`. This enables bootstrapping the `hmf4j-conf.yaml`

### Sending Messages

The first step towards sending a message is to register the Producer bean. This can be done as follows:

```java
@Component
public class SampleProducer extends MessageProducerFoundation {

    @Override
    protected String getQueueName() {
        return "test_topic";
    }

    @Override
    protected String getProfile() {
        return "profile1";
    }

    public SampleProducer() throws InterruptedException {
        super();
    }
}
```

- The class `SampleProducer` is inherting `MessageProducerFoundation`. The `MessageProducerFoundation` class performs all the wiring that is needed to setup a producer. 
- As seen above, the `getProfile()` function tells the producer which profile from the `hmf4j-conf.yml` has to be used for setting up the producer. In this case we are using `profile1`, which per the configuration uses the `system` as `kafka`. This means that the `SampleProducer` wired up to send messages to a Kafka messaging system. 

The `SampleProducer` is configured to write to the topic `test_topic` per the `getQueueName()` function

Next step is to use the above producer to send a message. The below code illustrates that:

```java
@Component
public class ProducerExec {

    public ProducerExec(SampleProducer producer){
        SimpleMessage contentMsg = new SimpleMessage();
        contentMsg.setContentSchema(String.class.toString());
        contentMsg.setDateTime(Optional.of(Date.from(Instant.now())));
        contentMsg.setMessageId("msg-1");
        contentMsg.setPayload("Hello, this is a sample message".getBytes());
        contentMsg.setSender("app-1");
        producer.sendMessage(contentMsg);
        System.out.println("Message sent");
    }
}
```

The above `ProducerExec` class is a Spring Bean that while getting bootstrapped uses the `SampleProducer` that has been defined before to send a message to the Kafka 
