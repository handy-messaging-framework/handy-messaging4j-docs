---
title: Memcell Messaging System
layout: default
parent: Features
---

> Note: As of hmf4j version `1.1.0` Photon Messaging System has been deprecated and has been superseded by Memcell Messaging System

# Memcell Messaging System
Before dwelving to the details of using the test toolkit, lets see a new messaging system that comes as a part of the Handy Messaging Framework - `Memcell Messaging System`
Memcell is an in-memory messaging system that is built from ground up with focus on testing messaging handler routines within an application. While testing a message producer or consumer, Memcell Messaging Service abstracts the messaging system and allows the testing code to send and receive messages from Memcell Messaging just like how they can do with any other messaging systems like Apache Kafka, Google Pub/Sub etc...
Memcell Messaging System derives its features from common behavior of several messaging service which is the core foundation on which the HMF4J is built upon. Just like how an application can send and receive asynchronous messages from any messaging system, it can do the same with Memcell Messaging System.

## Importing Memcell Messaging

```xml
 <dependency>
    <groupId>io.github.handy-messaging-framework</groupId>
    <artifactId>hmf4j-memcell-messagingsystem</artifactId>
    <version>1.1.0</version>
</dependency>
```

## Why Memcell Messaging System?
Memcell Messaging Systems brings the common denominator of different messaging systems together, there are several characteristics that makes Memcell ideal for integrating with your test code:
- Running in the same memory space as that of your application
 One of the main charactersic of Memcell Messaging is, it runs in the same memory space as that of the application code that is getting tested. Unlike any other messaging systems, Memcell Messaging is not running in a different memory space as a different process. This makes request and response processing way faster than handling the same through a messaging system that runs in a different process space.

- Support for rasing multiple instances of messaging systems - If you have a scenario where you are interfacing with multiple messaging systems and want to test the interactions among them, Memcell Messaging has the capability to spin up multiple messaging services that can then be tied to the application tested.

- Seamless integration with HMF4J's Test Toolkit - HMF4J's Test Toolkit is built around the Memcell Messaging system. This means that to test the code, there is no need of any extra bootstrapping of the Memcell Messaging system as it will be taken care seamlessly by the Test Toolkit. Hence any bootstrapping steps like - raising messaging instances, creation of queues, subscribing to queues are all taken care behind the scene by the Test Tookit. More on this in [Seamless Testing]({{ BASE_PATH }}/handy-messaging4j-docs/features/seamless_testing.html)

## Working with Memcell Messaging System
Though HMF4J's Test Toolkit makes it easier to work with Memcell Messaging System, here are some steps to get you started:

### Setting up Memcell Messaging System: 

Here is an example of setting up a Memcell Messaging instance:

```java
// Create an instance of MemcellMessagingAdministrator
    MemcellMessagingAdministrator admin = new MemcellMessagingAdministrator();

// Register a new messaging service
    String serviceId = "myService";
    admin.registerMessagingService(serviceId);
```

### Creating and Managing Queues
Learn how to create and manage queues within Memcell Messaging Service

```java
String queueName = "myQueue";
CommandResponse response = admin.registerMessagingQueue(queueName, serviceId);

// Check the response status
if (response.getCommandExecutionStatus().equals(CommandExecutionStatus.SUCCESS)) {
    System.out.println("Queue registered successfully");
} else {
    System.out.println("Failed to register queue");
}
```

### Sending and Receiving Messages: 
 Understand the process of sending and receiving messages using Memcell Messaging. Once the Service and Queue are setup, the next step is to creating a subscribe for the queue. Here is an example of creating a subscriber and fetching messages from the queue.

#### Sending Messages

```java
 // Create an instance of MemcellMessagingProducer
String serviceId = "myService";
MemcellMessagingProducer producer = new MemcellMessagingProducer(serviceId);

// Create a message
SimpleMessage message = new SimpleMessage();
message.setPayload("Hello, World!".getBytes());

// Send the message to a queue
String queueName = "myQueue";
CommandResponse response = producer.sendMessage(queueName, message);

// Check the response status
if (response.getCommandExecutionStatus().equals(CommandExecutionStatus.SUCCESS)) {
    System.out.println("Message sent successfully");
} else {
    System.out.println("Failed to send message");
}
```


#### Receiving Messages

```java
String serviceId = "myService";
String queueName = "myQueue";
String consumerId = "myConsumer";
try(MemcellMessagingConsumer consumer = new MemcellMessagingConsumer(serviceId, queueName, consumerId, SimpleMessage.class)){
    // Read messages from the queue
    long waitDurationMillis = 1000; // wait for 1 second
    List<SimpleMessage> messages = consumer.readMessages(waitDurationMillis);

    // Print the messages
    for (SimpleMessage message : messages) {
        System.out.println("MessageId: " + message.getId());
    }
}
```