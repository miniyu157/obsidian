> 写这篇文章时，以下操作在 termux chroot archlinux 完成，可能和标准 Linux 环境有所不同

### 购买域名

注册域名服务商 porkbun 账号 https://porkbun.com/ ，信息不用填太详细

刚注册就购买域名的话，估计会立即退款，可能要等 24 小时左右

买完之后从域名管理里面开启 API 访问，获取一个 API 保存到安全的地方

### 配置 DDNS脚本

写一个脚本在 `~/update_ddns.sh`，用于上传 ipv6 到域名服务商

其中 TTL 告诉全世界的 DNS 解析器（比如 8.8.8.8 等），这个域名对应的 IP 地址可以在缓存中保存多久

```bash
#!/bin/bash

# --- 配置区域 ---
API_KEY="pk1_xxxxxxxxxxx"
SECRET_KEY="sk1_xxxxxxxxxx"
DOMAIN="你的域名"
TTL="600"
INTERFACE="wlan0"
# --- 修改结束 ---

# 统一的日志函数
# $1: 状态 (SUCCESS / ERROR)
# $2: 消息
print_log() {
    # ISO 8601 格式时间戳，更易于排序和阅读
    printf "[%s] %s: %s\n" "$(date '+%Y-%m-%d %H:%M:%S')" "$1" "$2"
}

# 1. 获取公网 IPv6 地址
CURRENT_IPV6=$(ip a show "${INTERFACE}" | grep 'inet6 .* scope global' | grep 'mngtmpaddr' | awk '{ split($2, a, "/"); print a[1] }')

if [ -z "${CURRENT_IPV6}" ]; then
    print_log "ERROR" "未能从 ${INTERFACE} 获取 IPv6 地址。"
    exit 1
fi

# 2. 准备 API Payloads
DELETE_PAYLOAD=$(cat <<EOF
{
    "apikey": "${API_KEY}",
    "secretapikey": "${SECRET_KEY}"
}
EOF
)

CREATE_PAYLOAD=$(cat <<EOF
{
    "apikey": "${API_KEY}",
    "secretapikey": "${SECRET_KEY}",
    "name": "",
    "type": "AAAA",
    "content": "${CURRENT_IPV6}",
    "ttl": "${TTL}"
}
EOF
)

# 3. 步骤一：删除所有匹配的旧 AAAA 记录
DELETE_RESPONSE=$(curl -s -X POST "https://api.porkbun.com/api/json/v3/dns/deleteByNameType/${DOMAIN}/AAAA" \
     -H "Content-Type: application/json" \
     --data "${DELETE_PAYLOAD}")

NON_FATAL_ERROR=""
if ! echo "${DELETE_RESPONSE}" | grep -q '"status":"SUCCESS"'; then
    # 记录一个非致命错误，它将被附加到最终的成功或失败日志中
    NON_FATAL_ERROR=" (DELETE_WARN: $(echo "${DELETE_RESPONSE}" | tr -d '\n\r'))"
fi

# 4. 步骤二：创建新的 AAAA 记录
CREATE_RESPONSE=$(curl -s -X POST "https://api.porkbun.com/api/json/v3/dns/create/${DOMAIN}" \
     -H "Content-Type: application/json" \
     --data "${CREATE_PAYLOAD}")

# 5. 最终输出 (单一出口)
if echo "${CREATE_RESPONSE}" | grep -q '"status":"SUCCESS"'; then
    print_log "SUCCESS" "${DOMAIN} -> ${CURRENT_IPV6}${NON_FATAL_ERROR}"
else
    # 将 curl 的 JSON 错误信息压缩到一行
    ERROR_MSG=$(echo "${CREATE_RESPONSE}" | tr -d '\n\r')
    print_log "ERROR" "创建记录失败: ${ERROR_MSG}${NON_FATAL_ERROR}"
    exit 1
fi
```

运行脚本测试一下

```bash
~/update_ddns.sh
```

**设置定时运行** DDNS 脚本

安装 Cronie 服务

```bash
sudo pacman -S cronie
```

启动 crond 服务

```bash
sudo crond
```

编辑定时任务列表

```bash
EDITOR=nvim crontab -e
```

添加行

```
*/10 * * * * /home/yumeka/update_ddns.sh >> /home/yumeka/ddns_update.log 2>&1
```

> Crontab 语法解释
> 
> `*/10`：(分钟) 每 10 分钟。
> `*`：(小时) 每一个小时。
> `*`：(日期) 每一天。
> `*`：(月份) 每一个月。
> `*`：(星期) 每一周。
> 
> `/home/yumeka/update_ddns.sh >> /home/yumeka/ddns_update.log 2>&1`：要执行的命令，它会运行 DDNS 脚本并重定向输出到 `/home/yumeka/ddns_update.log`
