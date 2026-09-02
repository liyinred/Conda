# PostgreSQL 快速配置与局域网连接（精简版）

本文档说明在 Linux 上安装 PostgreSQL 后的常用配置步骤，并包含允许局域网（LAN）连接的要点。

## 速览
- 检查并启动服务
- 切换到 postgres 用户并进入 psql
- 创建数据库与用户并授权
- （可选）为 postgres 用户设置密码
- 开启外部连接（修改 postgresql.conf 和 pg_hba.conf）
- 配置防火墙并重启服务
- 客户端测试连接

---

## 1. 检查并启动服务

查看服务状态：

```bash
sudo systemctl status postgresql
```

如果未启动：

```bash
sudo systemctl start postgresql
```

---

## 2. 切换到默认管理员并进入 psql

切换用户并进入命令行：

```bash
sudo -i -u postgres
psql
```

退出 psql：

```sql
\q
```

---

## 3. 创建数据库和用户，并授权

在 psql 中执行：

```sql
CREATE DATABASE mydb;
CREATE USER myuser WITH ENCRYPTED PASSWORD 'mypassword';
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
```

根据需要替换 mydb、myuser、mypassword。

---

## 4. 为 postgres 用户设置密码（可选）

部分发行版安装时不会为 postgres 用户设置密码，如需设置：

```bash
sudo -i -u postgres
psql
ALTER USER postgres WITH PASSWORD 'your_new_password';
\q
```

若要通过密码登录，请在 pg_hba.conf 中将相应条目从 peer/ident 改为 md5 或 scram-sha-256（见下）。

---

## 5. 允许局域网连接（远程访问）

编辑 postgresql.conf：

```bash
sudo nano /etc/postgresql/<版本号>/main/postgresql.conf
```
将 `listen_addresses` 设置为 `'*'` 或指定服务器 IP：

```conf
listen_addresses = '*'
```

编辑 pg_hba.conf，允许局域网网段（例如 192.168.1.0/24）通过密码验证连接：

```bash
sudo nano /etc/postgresql/<版本号>/main/pg_hba.conf
```

添加或修改为：

```text
# 允许局域网通过密码登录
host    all    all    192.168.1.0/24    scram-sha-256
```

保存后重启服务（见第 7 步）。

---

## 6. 防火墙设置

如果使用 ufw，允许局域网访问 5432：

```bash
sudo ufw allow from 192.168.1.0/24 to any port 5432
sudo ufw reload
```

---

## 7. 重启服务

修改配置后重启使生效：

```bash
sudo systemctl restart postgresql
```

---

## 8. 客户端测试连接

在局域网另一台机器上：

```bash
psql -h <服务器IP> -U myuser -d mydb
```

示例：

```bash
psql -h 192.168.1.100 -U myuser -d mydb
```

---

## 9. 常见问题与安全提醒
- 验证失败：检查 postgresql.conf 的 listen_addresses、pg_hba.conf 条目和防火墙规则。
- 生产环境请限制允许连接的 IP 范围并使用强密码 / 客户端证书。
- 推荐使用 scram-sha-256（如果客户端支持），否则使用 md5 作为兼容选项。

---

以上为精简流程，覆盖常见操作与局域网连接所需的关键配置。若需针对特定发行版（例如 Ubuntu 20.04/22.04、Debian、CentOS）给出示例路径或故障排查步骤，我可以再补充。