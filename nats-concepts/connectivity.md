# 连接方式

NATS支持几种 _直接_ 连接到NATS服务器的方式。

* 单纯的NATS连接  
* TLS加密的NATS连接  
* [WebSocket](https://github.com/nats-io/nats.ws) NATS连接
* [MQTT](/running-a-nats-service/configuration/mqtt/) 客户端连接

There is also a number of adapters available to bridge traffic to and from other messaging systems  

还有许多适配器可用来桥接与其他消息传递系统之间的通信  

* [Kafka Bridge](https://github.com/nats-io/nats-kafka)  
* [JMS](https://github.com/nats-io/nats-jms-bridge) 也可以用来桥接MQ和RabbitMQ，因为它们都提供了一个JMS接口  
