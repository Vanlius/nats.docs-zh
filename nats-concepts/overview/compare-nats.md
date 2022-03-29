---
说明：'NATS与Kafka, Rabbit, gRPC等的比较'
---

# NATS对比

此功能比较是对当今几种流行消息传递技术中的一些主要组件的总结。这并不是一份详尽的清单，应彻底研究每种技术，以确定哪种技术最适合你。 

在此比较中，我们将重点介绍 NATS、Apache Kafka、RabbitMQ、Apache Pulsar 和 gRPC。  

## 语言和平台  

| 框架 | 客户端语言和平台 |
| :--- | :--- |
| **NATS** | core NATS: 48种已知的客户端类型，11种由维护者支持，18种由社区贡献。NATS streaming:维护者支持的7种客户端类型，社区贡献的4种。NAT server可以在Golang支持的架构上编译。NATS提供二进制发行版。 |
| **gRPC** | 13种客户端语言 |
| **Kafka** | 社区和 Confluent 支持 18 种客户端类型。 Kafka 服务器可以运行在支持 java 的平台上；非常广泛的支持。 |
| **Pulsar** | 7种客户端语言，5种第三方客户端-在macOS和Linux上测试。|
| **Rabbit** | 至少有10个客户端平台由维护者支持，有超过50种社区支持的客户端类型。支持的服务器平台包括:Linux Windows、NT。 |

## 内置模式

| 框架 | 支持的模式 |
| :--- | :--- |
| **NATS** | 通过内置的发布/订阅、请求/回复和负载均衡的队列订阅者模式实现流和服务。支持动态请求许可和请求主题混淆。 |
| **gRPC** | 每个通道一个服务，可能具有流式语义。服务的负载均衡可以在客户端或通过使用代理来完成。 |
| **Kafka** | 通过发布/订阅流式传输。可以通过消费者组实现负载均衡。应用程序代码必须在服务(请求/应答)模式的多个主题上将请求与应答关联起来。 |
| **Pulsar** | 通过发布/订阅流式传输。多个竞争消费者模式支持负载平衡。应用程序代码必须将请求与对服务（请求/回复）模式的多个主题的回复相关联。 |
| **Rabbit** | 通过发布/订阅的流和具有直接回复功能的服务。负载均衡可以通过工作队列来实现。应用程序必须在服务(请求/应答)模式的多个主题上将请求与应答关联起来。 |

## 消息交付可达性保证

| 框架 | 服务质量 / 保证 |
| :--- | :--- |
| **NATS** | 在JetStream中最多一次，至少一次，仅一次。 |
| **gRPC** | 最多一次 |
| **Kafka** | 最少一次, 仅一次. |
| **Pulsar** | 最多一次, 最少一次, 仅一次. |
| **Rabbit** | 最多一次, 最少一次. |

## 多租户和共享

| 框架 | 多租户支持 |
| :--- | :--- |
| **NATS** | NATS通过帐户和定义共享流和服务，支持真正的多租户和分散式安全。 |
| **gRPC** | N/A |
| **Kafka** | 不支持多租户 |
| **Pulsar** | 多租户是通过租户实现的;不支持跨租户的内置数据共享。每个租户可以有自己的认证授权方案。 |
| **Rabbit** | 虚拟主机支持多租户；不支持数据共享。 |

## 身份认证

| 框架 | 认证 |
| :--- | :--- |
| **NATS** | NATS支持TLS、NATS凭证、NKEYS (NATS ED25519 密钥)、用户名和密码或简单令牌。 |
| **gRPC** | TLS、ALT、令牌、通道和调用凭证，以及插件机制。 |
| **Kafka** | 支持Kerberos和TLS。支持JAAS和一个开箱即用的授权器实现，使用ZooKeeper存储连接和主题。 |
| **Pulsar** | TLS认证，Athenz, Kerberos, JSON Web令牌认证。 |
| **Rabbit** | TLS、SASL、用户名和密码，以及可插拔的授权。 |

## 授权

| 框架 | 认证 |
| :--- | :--- |
| **NATS** | 帐户限制，包括连接数、消息大小、导入和导出数。用户级的发布和订阅权限、连接限制、CIDR地址限制和时间限制。 |
| **gRPC** | 用户可以配置调用凭据，对服务上的细粒度单独调用进行授权。|
| **Kafka** | 支持JAAS，为丰富的Kafka资源集提供acl，包括主题、集群、组等。 |
| **Pulsar** | 对于诸如生产和消费的操作列表，可以将权限授予特定角色。 |
| **Rabbit** | ACL规定了对交换、队列、事务等资源的配置、写入和读取操作的权限。身份验证是可插拔的。|

## 消息保留和持久化

| 框架 | 消息保留和持久化支持 |
| :--- | :--- |
| **NATS** | 支持内存、文件和数据库持久性。消息可以按时间、计数或序列号重播，并且支持持久订阅。使用NATS流，脚本可以将旧的日志段归档到冷存储中。 |
| **gRPC** | N/A |
| **Kafka** | 支持基于文件的持久性。可以通过指定偏移量重放消息，并且支持持久订阅。支持日志压缩以及KSQL。 |
| **Pulsar** | 支持分层存储，包括文件、Amazon S3 或 Google Cloud Storage (GCS)。 Pulsar 可以从特定位置重放消息并支持持久订阅。支持 Pulsar SQL 和主题压缩，以及 Pulsar 函数。 |
| **Rabbit** | 支持基于文件的持久性。Rabbit支持基于队列的语义(相对于日志)，所以没有消息重放可用。 |

## 高可用和容错

| 框架 | HA和FT支持 |
| :--- | :--- |
| **NATS** | Core NATS 支持具有自我修复功能的全网状集群，为客户端提供高可用性。 NATS 流具有两种模式（FT 和完整集群）的热故障转移备份服务器。 JetStream 通过内置镜像支持水平可扩展性。 |
| **gRPC** | 不适用。 gRPC 依赖于 HA/FT 的外部资源。 |
| **Kafka** | 完全复制的集群成员通过Zookeeper进行协调。 |
| **Pulsar** | Pulsar支持集群代理与地理复制。 |
| **Rabbit** | 集群支持通过联合插件进行完整的数据复制。集群需要网络分区少且低延迟网络。 |

## 部署

| 框架 | 支持的部署模式 |
| :--- | :--- |
| **NATS** | NATS网络组件(服务器)是一个小型的静态二进制文件，可以部署在任何地方，从云中的大型实例到资源受限的设备(如Raspberry PI)。NATS支持Adaptive Edge架构，该架构允许大规模、灵活的部署。单个服务器、叶节点、集群和超集群(集群中的集群)可以以任何方式组合，以实现非常灵活的部署，适用于云计算、内部部署、边缘和物联网。客户端不知道拓扑，可以连接到部署中的任何NATS服务器。 |
| **gRPC** | gRPC是点对点的，没有需要部署或管理的服务器或代理，但总是需要额外的组件用于生产部署。 |
| **Kafka** | Kafka支持集群，镜像到松散耦合的远程集群。客户端被绑定到集群中定义的分区上。Kafka服务器需要一个JVM, 8个核，64gb到128gb的RAM，两个或更多的8tb SAS/SSD磁盘，和一个10g的网卡。 [_\(1\)_](compare-nats.md#references)__ |
| **Pulsar** | Pulsar 支持集群和集群之间的内置异地复制。客户端可以连接到具有适当配置的租户和命名空间的任何集群。Pulsar需要JVM，并且需要至少6节点的Linux机器或VM。3节点运行ZooKeeper。3节点运行Pulsar broker和 BookKeeper bookie。 [_\(2\)_](compare-nats.md#references)__ |
| **Rabbit** |Rabbit通过federation插件支持集群和跨集群消息传播。客户端不知道拓扑并且可以连接到任何集群。服务器需要Erlang虚拟机和依赖项。 |

## 监控

| 框架 | 监控工具 |
| :--- | :--- |
| **NATS** | NATS支持将监控数据导出到Prometheus，并拥有Grafana仪表板来监控和配置警报。还有一些开发监视工具，如nats-top。支持强大的sidecar部署或与NATS测量器简单的连接和查看模型。 |
| **gRPC** | 需要外部组件(如服务网格)来监视gRPC。 |
| **Kafka** | Kafka 有许多管理工具和控制台，包括 Confluent Control Center、Kafka、Kafka Web Console、Kafka Offset Monitor。 |
| **Pulsar** | CLI 工具、每个主题的仪表板和第三方工具。 |
| **Rabbit** | CLI工具，一个基于插件的管理系统，带有仪表板和第三方工具。 |

## 管理

| 框架 | 管理工具 |
| :--- | :--- |
| **NATS** | NATS将操作与安全分开。部署中的用户和帐户管理可以通过CLI分散和管理。服务器(网络元素)配置通过命令行和配置文件与安全性分离，可以在运行时通过更改重新加载配置文件。 |
| **gRPC** | 管理gRPC需要外部组件，比如服务网格。 |
| **Kafka** | Kafka 有许多管理工具和控制台，包括 Confluent Control Center、Kafka、Kafka Web Console、Kafka Offset Monitor。 |
| **Pulsar** | CLI工具、主题指示板和第三方工具。 |
| **Rabbit** | CLI工具，一个基于插件的管理系统，带有仪表板和第三方工具。 |

## 集成

| 框架 | 内置第三方集成 |
| :--- | :--- |
| **NATS** | NATS 支持 WebSockets、Kafka 桥接器、IBM MQ 桥接器、Redis 连接器、Apache Spark、Apache Flink、CoreOS、Elastic、Elasticsearch、Prometheus、Telegraf、Logrus、Fluent Bit、Fluentd、OpenFAAS、HTTP 和 MQTT 等等。 [more](https://nats.io/download/#connectors-and-utilities). |
| **gRPC** | 有许多第三方集成，包括 HTTP、JSON、Prometheus、Grift 等。 [_\(3\)_](compare-nats.md#references)__ |
| **Kafka** | Kafka 在其生态系统中有大量的集成，包括流处理（Storm、Samza、Flink）、Hadoop、数据库（JDBC、Oracle Golden Gate）、搜索和查询（ElasticSearch、Hive），以及各种日志记录和其他集成. |
| **Pulsar** | Pulsar 有许多集成，包括 ActiveMQ、Cassandra、Debezium、Flume、Elasticsearch、Kafka、Redis 等。 |
| **Rabbit** | RabbitMQ 有许多插件，包括协议（MQTT、STOMP）、WebSockets 以及各种授权和身份验证插件。 |

## References

1.  [https://docs.cloudera.com/HDPDocuments/HDF3/HDF-3.1.0/bk_planning-your-deployment/content/ch_hardware-sizing.html](https://docs.cloudera.com/HDPDocuments/HDF3/HDF-3.1.0/bk_planning-your-deployment/content/ch_hardware-sizing.html)
2. [https://pulsar.apache.org/docs/v1.21.0-incubating/deployment/cluster/](https://pulsar.apache.org/docs/v1.21.0-incubating/deployment/cluster/)
3. [https://github.com/grpc-ecosystem](https://github.com/grpc-ecosystem)

