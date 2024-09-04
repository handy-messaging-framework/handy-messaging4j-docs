---
layout: default
title: Photon Message Queue Integration
parent: Supported Messaging Platforms
---

# Photon Messaging Integration

The Photon Messaging Connector enables the framework to interface with Photon's messaging system. The following dependency along with other Handy-Messaging framework libraries imports Photon connector:

```xml
<dependency>
  <groupId>io.github.handy-messaging-framework</groupId>
  <artifactId>hmf4j-photon-connector</artifactId>
  <version>1.0.0</version>
</dependency>
```

The `system` parameter in the configuration profile should be `photon-mq`. The supported `producer` and `consumer` properties are given below:

### Producer Properties

| Property | Semantics | DataType
| -------- | --------- | ------------
| photon.messaging.instance | The instance of the Photon messaging service | String

### Consumer Properties

| Property | Semantics | DataType
| -------- | --------- | ------------
| photon.messaging.instance | The instance of the Photon messaging service | String
| application.id | The application id for the consumer | String
| max.messages.per.batch | Mandatory Handy-Messaging Framework's dispatcher property | Integer
| max.poll.duration.millis | Mandatory Handy-Messaging Framework's dispatcher property | Integer

A sample configuration file illustrating the producer and consumer properties is given below:

```yaml
hmf4j:
 profiles:
  - profileName: photon_profile
    system: photon
    consumer:
      properties:
        photon.messaging.instance: "myPhotonInstance"
        application.id: "myAppId"
        max.messages.per.batch: 3
        max.poll.duration.millis: 15000
    producer:
      properties:
        photon.messaging.instance: "myPhotonInstance"
```
