# 演示参考

我们已经为您提供了参考演示，您可以自己尝试 NATS（和 JetStream）。为了跟进演示，您可以选择以下选项之一：

- 已经安装`nats` CLI工具，并安装本地nats服务器(也可以使用可访问的远程服务器)。  
- 您可用使用Synadia's NGS。 
- 您甚至可以使用安装 NATS 的演示服务器。可以通过`nats://demo.nats.io`访问（这是一个 NATS 连接 URL；不是浏览器 URL。您将它传递给 NATS 客户端应用程序）。
  
## 安装[`nats`](/using-nats/nats-tools/nats_cli/readme.md) CLI 工具

MacOS:

```shell
brew tap nats-io/nats-tools
brew install nats-io/nats-tools/nats
```

Arch Linux:

```shell
yay natscli
```
    
对于其他版本的 Linux 和 Windows： .deb 或 .rpm 文件和 Windows 二进制文件（甚至适用于 ARM）在 [GitHub Releases](https://github.com/nats-io/natscli/releases)中可用。

## 本地安装NATS服务器 (如需要)

如果要在本地运行服务器，则需要先安装并启动它。或者，如果您已经知道如何在远程服务器上使用 NATS，则只需使用`-s`参数将服务器 URL 传递给`nats`，或者最好使用 `nats context add` 创建上下文，以指定服务器URL(s)和包含用户JWT的凭据文件。

### 通过包管理器安装NATS服务器

Mac OS:

```shell
brew install nats-server
```

Windows:

```shell
choco install nats-server
```

Arch Linux:

```shell
yay nats-server
```
  
对于其他版本的 Linux 或其他架构，您可以安装[发布版本](https://github.com/nats-io/nats-server/releases)，如下所示
  
### 下载发布版本

您可以在此处找到最新版本的 `nats-server` [here](https://github.com/nats-io/nats-server/releases)。

您可以手动下载与您的系统架构匹配的 zip 文件，然后将其解压缩。您还可以使用 curl 下载特定版本。例如，下面的示例显示了如何下载 Linux AMD64 的 `nats-server` 2.6.2 版本：

```shell
curl -L https://github.com/nats-io/nats-server/releases/download/v2.6.5/nats-server-v2.6.5-linux-amd64.zip -o nats-server.zip
```

```shell
unzip nats-server.zip -d nats-server
```
应该输出如下类似
```text
Archive:  nats-server.zip
   creating: nats-server-v2.6.2-linux-amd64/
  inflating: nats-server-v2.6.2-linux-amd64/README.md
  inflating: nats-server-v2.6.2-linux-amd64/LICENSE
  inflating: nats-server-v2.6.2-linux-amd64/nats-server
```
最后，将其复制到 bin 文件夹（这允许您从系统中的任何位置运行可执行文件）：
```shell
sudo cp nats-server/nats-server-v2.6.2-linux-amd64/nats-server /usr/bin
```

### 启动NATS服务器 (如需要)

要在本地启动一个简单的演示服务器，只需运行：

```bash
nats-server
```

(或 `nats-server -m 8222` 如果要启用http监控功能)

服务器启动成功后，您将看到以下消息：

```text
[14524] 2021/10/25 22:53:53.525530 [INF] Starting nats-server
[14524] 2021/10/25 22:53:53.525640 [INF]   Version:  2.6.1
[14524] 2021/10/25 22:53:53.525643 [INF]   Git:      [not set]
[14524] 2021/10/25 22:53:53.525647 [INF]   Name:     NDAUZCA4GR3FPBX4IFLBS4VLAETC5Y4PJQCF6APTYXXUZ3KAPBYXLACC
[14524] 2021/10/25 22:53:53.525650 [INF]   ID:       NDAUZCA4GR3FPBX4IFLBS4VLAETC5Y4PJQCF6APTYXXUZ3KAPBYXLACC
[14524] 2021/10/25 22:53:53.526392 [INF] Starting http monitor on 0.0.0.0:8222
[14524] 2021/10/25 22:53:53.526445 [INF] Listening for client connections on 0.0.0.0:4222
[14524] 2021/10/25 22:53:53.526684 [INF] Server is ready
```

NATS 服务器在 TCP 端口 4222 上侦听客户端连接。
