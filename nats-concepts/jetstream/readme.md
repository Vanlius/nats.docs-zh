# JetStream

## JetStream
 
NATS 有一个称为 JetStream 的内置分布式数据持久化系统，它在基本的“Core NATS”功能和服务质量之上启用新功能和更高质量的服务。  
JetStream 内置在 `nats-server` 中，你需要 1 个（或 3 或 5 个，如果你希望对 1 或 2 个同时发生 NATS 服务器故障的容错能力）启用 JetStream 即可应用到所有客户端应用程序。  
JetStream的创建是为了解决当前流技术中存在复杂性、脆弱性和缺乏可扩展性的问题。有些技术能更好地解决这些问题，但目前的流技术还没有真正的实现多租户、水平可扩展并支持多种部署模型。我们所知道的任何其他技术都不能在相同的安全环境下从边缘扩展到云，同时对操作具有完整的部署可观察性。  
### 目标  

JetStream的开发目标如下:

* 该系统必须易于配置和操作，并且易于观察。
* 系统必须符合NATS 2.0安全模型，保证系统的安全性和可操作性。
* 该系统必须横向扩展，并适用于高摄取率。
* 系统必须支持多用例。
* 系统必须支持自愈，且始终可用。
* 系统必须具有更接近core NATS 的 API。
* 系统必须允许NATS消息成为流的一部分。
* 系统必须显示与有效负载无关的行为。
* 系统不能存在第三方依赖关系。

## JetStream支持的功能  

### Streaming: 发布者和订阅者之间的时序解耦  

租户发布/订阅消息是一种发布者和订阅者之间存在的时间耦合：订阅者仅接收主动连接到消息系统时发布的消息。消息传递系统提供发布者和订阅者之间时序解耦的传统方式是通过“持久化订阅者”，或有时候采用“队列”的方式，但这两种方式都不完美:

* 需要在发布消息之前创建持久订阅者  
* 队列用于对消息的工作负载分配和消费，而不是用作消息重放的机制。  

然而，现在已经设计出一种提供时序解耦的新方法并已成为“主流”：streaming。流获取发布（存储）在一个（或多个）主题上的消息，允许客户端应用程序随时创建“订阅者”（即 JetStream 消费者）以重放（replay）或消费（consume）的方式读取存储在流中的所有或部分消息。  

#### 重放策略  

JetStream消费者支持多个重放策略，取决于消费应用程序是否想要接收:

* 当前存储在流中的所有消息，这意味着完整的消息“重放”，你可以选择“重放策略”（即重放的速度）为： 
  * 实时(意思是消息以尽可能快的速度传递给消费者)
  * 原始(意思是消息以它们被发布到流中的速度传递给消费者，这可能非常有用，例如对于分段生产流量)
* 存储在流中的最后一条消息，或每个主题的最后一条消息(因为流可以捕获多个主题)
* 从一个指定的序列号开始
* 从指定的开始时间开始

#### 流的保留策略和限制

在'Core NATS'基础上实现了新的功能和更高的服务质量。实际上，流不能“永远”增长，因此JetStream支持多种数据保留策略，并能够对流的大小施加限制。  

**限制**

你可以对流（stream）做以下的限制

* 消息在流中的最长生命周期  
* 流的总大小（以byte为单位）  
* 消息在流中保存的最大条数  
* 单条消息的最大大小  
* 你可以指定一个丢弃策略:当达到一个限制，一个新消息被发布到流中时，你可以选择丢弃当前流中最早的或最新的消息，以便为新消息腾出空间。  
* 你还可以对任何给定时间点为流定义的消费者数量设置限制  

**保留策略**

你可以为每个流设置你想要的留存策略:  

* limits (默认)  
* _interest_ (只要流上有消费者，消息就会一直保存在流中)  
* _work queue_ (流用作共享队列，当消息被消费时，将从流中删除消息)  

### 分布式持久化存储

你可以根据需求以选择消息存储的持久性和弹性  

* Memory（内存存储）
* File（文件存储）
* Replication (多副本) (在nats服务器副本1 (none), 2, 3之间实现数据容错) 

JetStream使用NATS优化的RAFT分布式仲裁算法，在NATS服务器集群中做持久服务，即使面临拜占庭式的故障，也能立即保证服务的一致性。  

JetStream还可以为存储的消息提供加密。  

在JetStream中，存储消息的配置与如何消费消息的配置是分开定义的。存储消息是在流中定义，而消费消息是在消费者定义。   

#### Stream replication factor

流的复制因子(R，通常称为“Replicas”的数量)决定了它存储的位置数量， 允许你对其进行调优，以平衡资源使用和性能的风险。 易于重建或临时的流可能是基于内存的 R=1，而可以容忍一些停机时间的流可能是基于文件的 R-1。  

在一般情况下考虑节点故障和均衡性能的流，配置R=3。具有高弹性但性能较差且成本更高的配置是 R=5，即副本的最大限制值。   

Rather than defaulting to the maximum, we suggest selecting the best option based on use case behind the stream. This optimizes resource usage to create a more resilient system at scale.

我们建议基于流的实际用例来配置最佳选项，而不是默认采用最大副本。这优化了资源的使用，以创建一个更有弹性规模的系统。

* Replicas=1 - 在提供stream服务的服务器中断（故障）期间无法运行。高性能。
* Replicas=2 - 目前没有明显的好处。我们建议使用Replicas=3代替。
* Replicas=3 - 可以容忍丢失一台为流提供服务的服务器。风险与性能之间的理想平衡。
* Replicas=4 - 与Replicas=3相比，除了在5个节点的集群中略有不同外，没有显著的好处。
* Replicas=5 - 能够容忍同时丢失两台流服务器。以牺牲性能为代价降低风险。  

#### 流镜像

JetStream还允许服务器管理员轻松地对流做镜像，例如在不同的JetStream域之间提供容灾。你还可以将一个流定义为另一个流的数据源。  
### De-coupled流控制

JetStream提供了对流的解耦合流控制，流控制不是“端到端”的，在这种流控制中，发布者被限制发布的速度不超过所有消费者(即最小公分母)可以接收的最慢速度，而是在每个客户端应用程序(发布者或消费者)和nats服务器之间单独发生。

当使用JetStream publish调用来发布消息到流时，在发布者和nats服务器之间有一个确认机制，并且你可以选择同步或异步（'批处理')JetStream调用方式发布。  

在订阅端，客户端应用程序发送消息或接收、消费nats服务器中流的消息也同样受到流控制。 

### 仅一次消息投递（交付）  

因为使用JetStream流的发布的消息会被服务器确认，流提供的消息确认为“至少一次”，这意味着，投递的消息可靠且不会产生重复，但有一些特定场景会失败，可能导致消息发布应用程序(错误地)认为消息没有成功发布，因此再次发布消息，还有一些失败的情况可能导致客户端应用程序的消费确认（ack）丢失，从而导致消息被服务器重新发送给消费者。    

因此，JetStream也提供了“exactly once”语义。对于发布者，它依赖发布应用程序在消息头中附加一个唯一的消息或publish id，服务器上在配置的滚动时间段内跟踪这些ID，以便检测发布者两次发布相同的消息。对于订阅者，使用双重确认机制来避免消息在某些类型的故障后产生的消息重发。  

### 消费者

JetStream消费者是一个流上的“视图”，它们被客户端应用程序订阅(或拉取)来接收存储在流中的消息副本(或如果流设置为工作队列，则使用)。  
#### Fast push consumers

客户端应用程序可以选择使用消息非确认的推模式消费者，以最快的方式（选择的replay策略）接收指定投递主题或inbox中的消息，这些消费者是用来的'重放'消息而不是消费流中的消息。  

#### 批量处理功能的水平伸缩拉（模式）消费者

客户端应用程序还可以使用和共享需求驱动的拉消费者，支持批处理，并且必须确认消息接收和处理，这意味着它们可以用于消费（即使用流作为分布式队列）以及处理在流中的消息。  
拉模式消费者应该在应用程序之间共享（就像队列组一样），以便为流中消息的处理或消费提供简单透明的水平可伸缩性，而不必担心定义分区或担心容错。  
注意：使用拉消费者并不意味着你不能将更新（发布到流中的新消息）消息实时“推送”到你的应用程序，因为你可以将（合理的）超时传递给消费者的 Fetch 调用，和调用它在一个循环中。     

#### 消费者确认机制
 
虽然你可以使用不确认消息语义的消费者以换取消息传递的速率，但大多数处理都不是幂等的，你需要更高质量的消息投递语义(例如自动从可能导致某些消息未被处理或被处理多次的各种故障场景中恢复的功能)和使用带消息确认的消费者。JetStream支持一种以上的消息确认:  

* 消费者支持所有的消息确认，直到序列号全部被确。消费者提供最高质量的服务，但需要明确确认每条消息的接收和处理，以及服务器在重新传递特定消息之前等待确认的最长时间（发送到附加到消费者）  
* 你可以发回拒绝/否定确认  
* 你甚至可以发送正在处理的确认（以表明你仍在处理有问题的消息并且需要更多时间才能确认或确认它）  

### K/V 存储

JetStream是一个持久层，而流只是构建在该层之上的功能之一。
另一个功能(通常在消息传递系统中不可用，甚至与消息传递系统毫不相关)是JetStream键/值存储:存储、检索和删除与键关联的值消息的能力，监视(监听)发生在该键上的更改，甚至检索特定键上发生的值(和删除)的历史记录。

**Legacy**

Note that JetStream completely replaces the [STAN](/legacy/stan/README.md) legacy NATS streaming layer.
