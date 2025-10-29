首先从 Google Play 下载 AFWall+

启动脚本

```bash
# 定义 ip6tables 命令的路径
IP6TABLES=/system/bin/ip6tables

# --- 1. 清空现有规则并设置默认策略 ---
# 清空 INPUT (入站) 和 FORWARD (转发) 链
$IP6TABLES -F INPUT
$IP6TABLES -F FORWARD

# 设置默认策略：拒绝所有入站和转发流量
$IP6TABLES -P INPUT DROP
$IP6TABLES -P FORWARD DROP
# 允许所有出站流量 (服务器需要对外响应)
$IP6TABLES -P OUTPUT ACCEPT

# --- 2. 添加基础允许规则 ---
# 允许 loopback 接口 (::1) 的所有通信
$IP6TABLES -A INPUT -i lo -j ACCEPT

# 允许已建立和相关的连接 (非常重要，允许服务器响应已连接的客户端)
$IP6TABLES -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 允许 ICMPv6 (IPv6 正常工作所必需，例如邻居发现)
$IP6TABLES -A INPUT -p icmpv6 -j ACCEPT

# --- 3. 开放你指定的服务器端口 (TCP) ---
# 允许 80 (HTTP), 443 (HTTPS), 37781
$IP6TABLES -A INPUT -p tcp -m multiport --dports 80,443,37781 -j ACCEPT
```

关闭脚本

```bash
# 定义 ip6tables 命令的路径
IP6TABLES=/system/bin/ip6tables

# --- 1. 恢复默认策略为 "允许所有" ---
$IP6TABLES -P INPUT ACCEPT
$IP6TABLES -P FORWARD ACCEPT
$IP6TABLES -P OUTPUT ACCEPT

# --- 2. 清空所有链的规则 ---
$IP6TABLES -F INPUT
$IP6TABLES -F FORWARD
$IP6TABLES -F OUTPUT
```