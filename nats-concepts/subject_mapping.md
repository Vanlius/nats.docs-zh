# 主题映射和流量控制  

主题映射是NATS服务器的一个非常强大的特性，对于canary部署、A/B测试、混沌测试和迁移到新的主题命名空间非常有用。

## 简单映射  

`foo:bar` 的例子很简单。服务器接收到的所有消息主题foo都被映射，可以被订阅到bar的客户端接收。

## 主题标记重排序

通配符可以通过`$<position>`引用。例如，第一个通配符是$1，第二个是$2，以此类推。引用这些令牌可以允许重新排序。

例如，使用此映射`bar.*.*: baz.$2.$1`，最初发布到`bar.a.b`的消息在服务器中重新映射到`baz.b.a`。在`bar.one.two`上到达服务器的消息将被映射到`baz.two.one`，依此类推。

## A/B测试或金丝雀发布的加权映射

流量可以按百分比从一个主题分割到多个主题。下面是金丝雀部署的一个示例，从你的服务的版本1开始。

应用程序将在myservice.requests中请求服务。执行服务器工作的响答者将订阅`myservice.requests.v1`。你的配置看起来像这样:

```
  myservice.requests: [
    { destination: myservice.requests.v1, weight: 100% }
  ]
```

对`myservice.requests`的所有请求都将转发到你版本为1的服务。  

当版本2出现时，你将希望使用金丝雀部署对其进行测试。版本2将订阅`myservice.requests.v2`。启动服务实例。  

更新配置文件以将向`myservice.requests`发出的部分请求重定向到你的服务的版本 2。    
 
例如，下面的配置意味着 98% 的请求将发送到版本 1，2% 的请求将发送到版本 2。

```
    myservice.requests: [
        { destination: myservice.requests.v1, weight: 98% },
        { destination: myservice.requests.v2, weight: 2% }
    ]
```

一旦你确定版本2稳定，你可以将100%的流量切换到它，然后你可以关闭你的服务的版本1实例。  

## 测试流量控制 

流量控制在测试中也很有用。你可能有一个在 QA 中运行的服务，它模拟可能接收 20% 的流量以测试服务请求者的故障场景。   

`myservice.requests.*: [{ destination: myservice.requests.$1, weight: 80% }, { destination: myservice.requests.fail.$1, weight: 20% }`

## 人为损失（Artificial Loss）

或者，通过将一定百分比的流量映射到同一主题，将损失引入系统以进行混沌测试。在这个极端的例子中，发布到`foo.loss.a`的 50% 的流量会被服务器人为地丢弃。  

`foo.loss.>: [ { destination: foo.loss.>, weight: 50% } ]`

你既可以拆分也可以引入损失进行测试。在这里，90%的请求会发送给你的服务，8%会发送给模拟故障情况的服务，而未说明的2%将模拟消息丢失。  

`myservice.requests: [{ destination: myservice.requests.v3, weight: 90% }, { destination: myservice.requests.v3.fail, weight: 8% }]`  
剩下的 2% “丢失”
