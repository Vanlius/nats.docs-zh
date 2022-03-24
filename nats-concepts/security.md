# 安全

NATS有很多安全特性:  

* 连接可以使用 [_TLS加密_ ](/running-a-nats-service/configuration/securing_nats/tls.md)
* 客户端连接可以通过多种方式进行[_身份验证_](../running-a-nats-service/configuration/securing_nats/auth_intro/) :
  * [Token Authentication](../running-a-nats-service/configuration/securing_nats/auth_intro/tokens.md)
  * [Username/Password credentials](../running-a-nats-service/configuration/securing_nats/auth_intro/username_password.md)
  * [TLS Certificate](../running-a-nats-service/configuration/securing_nats/auth_intro/tls_mutual_auth.md)
  * [NKEY with Challenge](../running-a-nats-service/configuration/securing_nats/auth_intro/nkey_auth.md)
  * [Decentralized JWT Authentication/Authorization](../running-a-nats-service/configuration/securing_nats/jwt/)
* 经过身份验证的客户端被标识为用户，并具有一组 [_授权_](../running-a-nats-service/configuration/securing_nats/authorization.md)

您可以将[帐户](../running-a-nats-service/configuration/securing_nats/accounts.md)用于多租户：每个帐户都有自己独立的“主题命名空间”，您可以控制帐户之间消息流和服务的导入/导出，以及客户端应用程序可以作为身份验证的任意数量的用户。允许用户发布和/或订阅的主题或主题通配符可以通过服务器配置或作为签名 JWT 的一部分进行控制。  

JWT 身份验证/授权管理是去中心化的，因为每个帐户私钥持有人都可以自行管理其用户和授权，而无需通过创建自己的 JWT 并将其分发给用户来更改 NATS 服务器上的任何配置。 NATS 服务器不需要存储任何用户私钥，因为它们只需要验证客户端应用程序提供的用户 JWT 中包含的信任签名链，以验证它们是否拥有该用户的正确公钥。

NATS 的 JetStream 持久层还提供[静态加密](../running-a-nats-service/nats_admin/jetstream_admin/encryption_at_rest.md)。
