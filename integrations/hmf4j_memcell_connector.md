---
layout: default
title: Memcell Message Queue Integration
parent: Supported Messaging Platforms
---

# Memcell Messaging Integration

The Memcell Messaging Connector enables the framework to interface with Memcell's messaging system. The following dependency along with other Handy-Messaging framework libraries imports Memcell Messaging connector:

```xml
<dependency>
  <groupId>io.github.handy-messaging-framework</groupId>
  <artifactId>hmf4j-memcell-connector</artifactId>
  <version>1.1.0</version>
</dependency>
```

The `system` parameter in the configuration profile should be `memcell-mq`. The supported `producer` and `consumer` properties are given below:

### Producer Properties

| Property | Semantics | DataType
| -------- | --------- | ------------
| memcell.messaging.instance | The instance of the Memcell messaging service | String

### Consumer Properties

| Property | Semantics | DataType
| -------- | --------- | ------------
| memcell.messaging.instance | The instance of the Memcell messaging service | String
| application.id | The application id for the consumer | String
| max.messages.per.batch | Mandatory Handy-Messaging Framework's dispatcher property | Integer
| max.poll.duration.millis | Mandatory Handy-Messaging Framework's dispatcher property | Integer

A sample configuration file illustrating the producer and consumer properties is given below:

```yaml
hmf4j:
 profiles:
  - profileName: memcell_profile
    system: memcell-mq
    consumer:
      properties:
        memcell.messaging.instance: "myMemcellInstance"
        application.id: "myAppId"
        max.messages.per.batch: 3
        max.poll.duration.millis: 15000
    producer:
      properties:
        memcell.messaging.instance: "myMemcellInstance"
```
