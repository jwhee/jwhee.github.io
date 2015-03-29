---
layout: post
title:  "RabbitMQ FAQs"
date:   2015-03-20 17:30:00
categories: blog
---
RabbitMQ is a solution that implements the [Advanced Messaging Queuing Protocol (AMQP)](http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) standard, and I have been using it a lot for my infrastructure. For any infrastructure team, I would definitely recommend having one instance of RabbitMQ spinned up in your environment, even if you don't need it at the moment. In the event when you need a robust queuing system, you can then hop right into using RabbitMQ with its [abundance of libraries](https://www.rabbitmq.com/devtools.html) in various languages.

For tutorials, you may look [here](https://www.rabbitmq.com/getstarted.html). This blog post is only to answer a few frequently asked questions when working with RabbitMQ.

### What are the benefits of using RabbitMQ/AMQP?
1. **Message guarantee**
If a message is published into a queue, it is guaranteed to be processed and ACKnowledged by a consumer. If a message is not ACK'ed, it will be requeued and reprocessed. Furthermore, if a message is tagged with the "Persistent" attribute, it will be stored on disc so that it will survive through server restart. This is especially handy if you are handling high volumns of purchasing and refund jobs, because you do not want to miss a customer's refund, trust me.

2. **Externalized state**
You can externalize your queues in memory into RabbitMQ. This way, your service becomes stateless, and you can more easily support rolling restarts of your service cluster. RabbitMQ will greatly help your infrastructure to provide high availability.

3. **Sophisticated routing**
RabbitMQ provides three default routing mechanisms (details in next section), and it can be easily extended to support custom needs.

4. **Round-Robin message distribution**
When multiple consumers are consuming from the same queue, RabbitMQ automatically distributes the messages in round-robin fashion. If a consumer fails to consume a message, the consumer will be marked as dead and messages will no longer be routed to that consumer.

In reality, RabbitMQ/AMQP provides a lot of features that one can easily implement, but why reinvent the wheel over and over? Having a generic, re-usable message queuing system is what RabbitMQ/AMQP provides.

### What are the differences between different exchange types?
There are three exchange types: fanout, direct, and topic. Here's the difference between them:

1. **fanout** - Your binding key is ignored.
2. **direct** - Your binding key is string-matched.
3. **topic** - Your binding key is RegEx-matched.

I'd say unless you want crazy complex routing logics, a direct exchange should satisfy most of your needs. If you find yourself needing a topic exchange, it might be a good time to ask if you are over-complicating your infrastructure.

### What does Durable mean?
You can declare exchange and queue with the "Durable" attribute. That just means that the exchange and queue will survive through server restart. However, the messages will not, unless the message is tagged with the "Persistent" attribute. Though it's a personal preference, I usually set "Durable" to true so that I know my exchange and queue will always be there once declared.

### What does Auto-Delete mean?
The "Auto-Delete" attribute allows exchange and queue to be automatically removed once they are done with their jobs. If an exchange is set with "Auto-Delete" attribute, the exchange will be deleted once its last bound queue is deleted. If a queue is set with "Auto-Delete" attribute, the queue will be deleted once its last bound consumer is deleted. Note that this only applies when the exchange/queue has at least one bindings, so you don't have to worry about losing the exchange/queue at creation time. This attribute is perfect for cases where your infrastructures need some use-and-throw-away exchanges and queues. In my experience, exchanges are rarely declared with "Auto-Delete", and queues are only declared "Auto-Delete" for test environment.

### How do you debug with RabbitMQ?
In my opinion, debugging with RabbitMQ is quite painful, and this is my main issue working with RabbitMQ. You cannot easily monitor what messages are in queue without being destructive. Using GetMessage and Requeue is a destructive action that may invalidates the original orders in the queue, so it is not a good approach. The usual approach to monitor a queue is to clone the queue and perform destructive actions on the cloned queue. This way, you may look into the content of the cloned queue and have a clue of what's going on, but at the same time the original queue is untouched. However, I really wish RabbitMQ provides one-click support to clone a queue, if this is how people debug their queues...