# Linux Docker 注意事项（简明提示）

## 概要
- 目的：在本机通过 Docker Compose 启动 newapi 服务时，避免与本地 mihomo tun 冲突，并检查 certbot、时间与 DNS、以及容器访问宿主机的常见问题。内容精简、可直接执行。

## 与 mihomo tun 冲突与解决（优先）
问题：通过 Docker Compose 启动的 newapi 可通过外网访问，仅在 mihomo 的 tun 模式关闭时可行。目标是让来自 Docker 桥接网络的“响应包”在主路由表查找，而不被 mihomo 覆盖；同时仍让 br-new-api 的出站流量（例如容器发起的远程 API 请求）继续走 mihomo tun（即只放行源端口为容器监听端口 3000 的响应）。

建议做法（仅包含关键命令）：
1. 在 docker-compose 中固定网络名为 `br-new-api`（见下节）。
2. 使用 iptables 标记满足条件的响应包，再用 ip rule 根据标记查主路由表（优先级高于 mihomo）：
   - 标记响应包（仅匹配源端口 3000 的 TCP 包，且来自 br-new-api 接口）：
     - iptables -t mangle -A PREROUTING -i br-new-api -p tcp --sport 3000 -j MARK --set-mark 1
   - 添加策略路由（将 mark=1 的包查主路由表，优先级设高）：
     - ip rule add fwmark 1 table main pref 100
   - 持久化：将上述 iptables 规则保存到系统的防火墙持久化文件（例如 /etc/iptables/rules.v4）或在 systemd unit 启动时重建；同样将 ip rule 加入启动脚本（/etc/rc.local 或 systemd unit）。

说明：直接用 ip rule 匹配端口不可行，所以用 mangle 表打 mark 是常见方案。上述方案仅把源端口为 3000 的“响应”走主路由表，其他通过 br-new-api 的流量仍会按默认路由（可能走 mihomo）。根据系统与 mihomo 的实际路由表优先级，`pref` 数值可调整。

## 固定 Docker Compose 网络为 br-new-api
在 docker-compose.yml 中，添加或修改 networks 段：

```yaml
networks:
  br-new-api:
    driver: bridge
    name: br-new-api
```

效果：保证桥接网名固定，便于在 iptables/规则中引用（接口名通常为 br-new-api）。

## 证书自动续期（certbot）分析与建议
要点：确认自动续期是否开启、续期后 nginx 是否需要重载、如何创建 deploy_hook、时区与时间同步、DNS 与 systemd-resolved 是否为持久化配置。

检查项与命令：
- 检查 certbot 定时/定时器：
  - systemctl list-timers --all | grep certbot
  - 或 systemctl status certbot.timer
- 是否需要 reload nginx：
  - 大多数情况下 certbot 续期后需要让 nginx 重新加载证书生效（reload 而非 restart 更常用）：
    - systemctl reload nginx
  - 推荐做法：使用 deploy hook 自动重载：
    - 创建文件 /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
    - 内容示例：
      - #!/bin/sh
      - systemctl reload nginx || true
    - chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
  - 如果使用 certbot 的系统级定时器，也可通过 certbot renew --deploy-hook "systemctl reload nginx" 来触发。
- 创建 deploy_hook（示例步骤）：
  - sudo tee /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh > /dev/null <<'EOF'
    #!/bin/sh
    systemctl reload nginx || true
    EOF
  - sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
- 检查系统时间与 NTP（是否为北京时间）：
  - timedatectl status
  - 若需设置为北京时间：sudo timedatectl set-timezone Asia/Shanghai
  - 检查 NTP 是否启用：timedatectl show -p NTPSynchronized --value 或 systemctl status systemd-timesyncd / chronyd
- 检查 DNS 配置与 systemd-resolved 持久性：
  - systemctl status systemd-resolved
  - ls -l /etc/resolv.conf  # 看是否为 systemd 的符号链接
  - 持久化修改 DNS：编辑 /etc/systemd/resolved.conf，设置 DNS=、FallbackDNS=，然后 sudo systemctl restart systemd-resolved；确保 /etc/resolv.conf 指向 /run/systemd/resolve/resolv.conf（或者调整为你希望的持久文件）。

## newapi 容器无法访问宿主机 127.0.0.1:8888（根因与解决）
问题分析：容器的 127.0.0.1 是容器自身的回环地址，指向容器内，而不是宿主机。要让容器访问宿主机上运行且监听 127.0.0.1:8888 的服务，需要采取下列任一方案：

选项：
- 在宿主机上将服务绑定到 0.0.0.0 或桥接网络地址（如 172.18.0.1:8888）：
  - 修改服务监听地址为 0.0.0.0:8888（最简单、推荐）。
- 使用宿主机桥接网关地址：
  - 找到桥接网关：docker network inspect br-new-api | grep Gateway
  - 在容器中访问：http://<Gateway_IP>:8888（例如 172.18.0.1:8888）
- 在 docker-compose 中加入 extra_hosts 映射（例如将 host.local 指向宿主机桥接地址）：
  - extra_hosts:
      - "host.docker.internal:172.18.0.1"
- 使用 host 网络模式（不隔离网络）：
  - docker run --network host ...  或 在 compose 中 network_mode: host（注意：会取消网络隔离，慎用）。
- 端口转发（若无法修改服务监听）：
  - 在宿主机上用 socat 或 iptables-NAT 将 127.0.0.1:8888 转发到 0.0.0.0:8888 或桥接地址。

简短示例：将服务改为监听 0.0.0.0 后，容器可直接访问 http://172.18.0.1:8888 或通过 extra_hosts 指向该地址。

## 小结（快速行动清单）
1. 在 docker-compose.yml 固定网络 name 为 br-new-api。 
2. 用 iptables mangle 打 mark（-i br-new-api -p tcp --sport 3000），并用 ip rule 将 mark=1 的包优先查 main 表（pref 100）。持久化规则。 
3. 为 certbot 创建 deploy hook 自动 reload nginx（/etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh），并确认 certbot.timer 是否启用。 
4. 确认系统时区为 Asia/Shanghai 且 NTP 启用（timedatectl）。
5. 检查 /etc/resolv.conf 与 systemd-resolved 状态，必要时在 /etc/systemd/resolved.conf 做持久配置并重启服务。 
6. 若容器需访问宿主服务，优先将服务绑定 0.0.0.0 或使用宿主桥接网关 IP（如 172.18.0.1）。

---
文件短小，供日常排障与配置复现使用。如需我可：
- 提供完整用于启动时加载 IP rules/iptables 的 systemd unit 示例；
- 帮你把上述更改写入 docker-compose.yml（需提供当前 compose 文件）。
