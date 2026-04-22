# FRP Intranet Tunnel Guide

本文档记录如何使用 FRP 将本机 `127.0.0.1:8000` 服务通过内网穿透映射到一台拥有公网 IP 的云服务器端口。

## 目标

将本机服务：

```text
127.0.0.1:8000
```

映射为外部可访问地址：

```text
http://云服务器公网IP:18000
```

整体链路如下：

```text
访问者 -> 云服务器公网IP:18000 -> frps -> frpc -> 本机127.0.0.1:8000
```

## 一、云服务器配置 FRP 服务端

以下示例假设云服务器系统为 Linux x86_64。

### 1. 下载 FRP

```bash
wget https://github.com/fatedier/frp/releases/download/v0.62.1/frp_0.62.1_linux_amd64.tar.gz
tar -xzf frp_0.62.1_linux_amd64.tar.gz
cd frp_0.62.1_linux_amd64
```

### 2. 创建服务端配置

创建 `frps.toml`：

```toml
bindPort = 7000

auth.method = "token"
auth.token = "换成一个足够复杂的随机字符串"
```

配置说明：

- `bindPort = 7000`：FRP 客户端连接服务端使用的端口。
- `auth.token`：客户端和服务端认证用的令牌，必须保持一致。

### 3. 启动服务端

```bash
./frps -c ./frps.toml
```

### 4. 放行云服务器端口

云服务器安全组和系统防火墙需要放行：

```text
7000/tcp   # frpc 连接 frps 使用
18000/tcp  # 对外访问本机 127.0.0.1:8000 使用
```

如果服务器使用 `ufw`：

```bash
sudo ufw allow 7000/tcp
sudo ufw allow 18000/tcp
```

## 二、本机配置 FRP 客户端

根据本机系统下载对应的 FRP 客户端。

Windows 可下载：

```text
frp_0.62.1_windows_amd64.zip
```

### 1. 创建客户端配置

创建 `frpc.toml`：

```toml
serverAddr = "你的云服务器公网IP"
serverPort = 7000

auth.method = "token"
auth.token = "和frps.toml里完全相同的token"

[[proxies]]
name = "local-8000"
type = "tcp"
localIP = "127.0.0.1"
localPort = 8000
remotePort = 18000
```

配置说明：

- `serverAddr`：云服务器公网 IP。
- `serverPort`：云服务器 `frps` 监听端口。
- `localIP`：本机服务监听地址。
- `localPort`：本机服务端口。
- `remotePort`：云服务器对外暴露端口。

### 2. 启动客户端

Windows PowerShell：

```powershell
.\frpc.exe -c .\frpc.toml
```

Linux 或 macOS：

```bash
./frpc -c ./frpc.toml
```

## 三、访问验证

确保本机服务已经运行：

```text
127.0.0.1:8000
```

然后从任意外部网络访问：

```text
http://你的云服务器公网IP:18000
```

如果本机 `127.0.0.1:8000` 是 HTTP 服务，浏览器可直接打开该地址。

## 四、后台运行建议

### 云服务器使用 systemd 管理 frps

建议将 FRP 放到 `/opt/frp`：

```bash
sudo mkdir -p /opt/frp
sudo cp frps frps.toml /opt/frp/
```

创建 `/etc/systemd/system/frps.service`：

```ini
[Unit]
Description=FRP Server
After=network.target

[Service]
Type=simple
WorkingDirectory=/opt/frp
ExecStart=/opt/frp/frps -c /opt/frp/frps.toml
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

启用并启动服务：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now frps
sudo systemctl status frps
```

### 本机 Windows 后台运行 frpc

Windows 可使用以下方式之一让 `frpc.exe` 后台运行：

- 任务计划程序
- NSSM
- Windows 服务管理工具

## 五、安全建议

- `auth.token` 不要使用弱口令，例如 `123456`。
- `remotePort` 不建议使用常见端口，除非确实需要。
- 云服务器只开放必要端口。
- 如果只给自己使用，建议在云服务器防火墙中限制 `18000` 的来源 IP。
- 不要将包含真实 token 的配置文件提交到公开仓库。

## 最小可用配置

云服务器 `frps.toml`：

```toml
bindPort = 7000

auth.method = "token"
auth.token = "your-strong-token"
```

本机 `frpc.toml`：

```toml
serverAddr = "your-server-public-ip"
serverPort = 7000

auth.method = "token"
auth.token = "your-strong-token"

[[proxies]]
name = "local-8000"
type = "tcp"
localIP = "127.0.0.1"
localPort = 8000
remotePort = 18000
```

访问地址：

```text
http://your-server-public-ip:18000
```
