---
layout: default
title: "Handy-Messaging-Framework4J"
nav_order: 1
---

# Handy-Messaging-Framework4J Documentation

## Motivation
Asynchronous messaging is one of the fundamental pillars of any distributed system. In this day and age, there is no scarcity of such asynchronous messaging platforms. Though these messaging systems have different implementation methods the core of what they offer pretty much stays the same - A publisher produces a message and one or more consumers can subscribe to that message though a topic or a queue. The consumers on receiving the message will process the message. In essence this fundamental behavior pretty much stays the same across most of these messaging systems. Though these messaging systems have plus and minus features to these fundamental behavior, there is no such standard framework for developers to interface with these messaging systems. This is the motivation for the Handy Messaging Framework.

## Overview
Handy-Messaging-Framework4J is a framework that abstracts the messaging layer from your application. It abstracts the details of how to interface with different messaging systems like Apache Kafka, Google Pubsub, MQTT etc... Thus the framework enbales you to focus on the core application details without spending the effort to intgrate with the messaging layer. This also enables you to seamlessly switch from one messaging service to another.
Apart from the core feature of standardizing the messaging layer, HMF4J provides the following features:
 - An extremely efficient dispatcher that provides the developer with different levels of flexibility in terms of handling the incoming data
 - Interoperability with multiple messaging systems seamlessly
 - Ordering of messages so as to avoid race condition scenarios
 - Standardized messaging types
 - Seamless testing of application using the packaged test toolkit and in-memory messaging system called Memcell Messaging System

To get started with the Handy-Messaging-Framework4J framework, check here - [Getting Started]({{ BASE_PATH }}/handy-messaging4j-docs/GettingStarted.html)

## Contact
For any queries, please reach out to [Aron Sajan Philip](https://aronsajan.github.io/)





