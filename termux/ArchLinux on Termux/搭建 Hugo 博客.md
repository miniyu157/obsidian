查看是否有公网 IPv6

https://www.test-ipv6.com/index.html.zh_CN

安装必要的软件包

```bash
sudo pacman -S nginx hugo
```

### 配置 Hugo

初始化博客

```bash
hugo new site yumeka-blog && cd yumeka-blog
```

初始化 git

```bash
git init -b main
```

添加 stack 主题

```bash
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/stack
```

获取公网 IPv6

```bash
ip a | rg 'inet6 .* scope global dynamic'
```

编辑 hugo.toml 写入以下内容

```bash
baseURL = "http://[2409:8a1a:537:9850:7840:abff:fe1b:9266]"
languageCode = "zh-CN"
title = "这里是 Yumeka ✨"
theme = "stack"
```

### 配置 Nginx

创建 nginx 网站根目录

```bash
sudo mkdir -p /srv/http/yumeka-blog
```

修正所有者

```bash
sudo chown -R yumeka:yumeka /srv/http/yumeka-blog 
```

生成静态网页

```bash
hugo -D -d /srv/http/yumeka-blog
```

编辑 /etc/nginx/nginx.conf，修改为以下内容

```
    server {
        # 侦听 IPv4 和 IPv6 的 80 端口
        listen       80;
        listen       [::]:80;

        # 接受所有域名/IP 的请求
        server_name  _;

        # 指向 Hugo 生成的 public 目录
        root   /srv/http/yumeka-blog;
        index  index.html index.htm;

        location / {
            # 确保 Hugo 的子页面能正确访问
            try_files $uri $uri/ =404;
        }

        # 保留默认的错误页面配置
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

```

运行 nginx（无 systemd）

```bash
# 检查配置语法
sudo nginx -t

# 启动 Nginx (仅在未运行时)
sudo nginx

# 如果你修改了配置，重载它
sudo nginx -s reload

# 停止 Nginx
sudo nginx -s stop
```

### 测试

**在本机中访问**
[http://localhost/](http://localhost/)

**在局域网中访问**
> ```bash
>  ip a | rg 'inet .* wlan0'
>  # 输出类似 inet 192.168.1.11/24 brd 192.168.1.255 scope global wlan0
>  ```
[http://192.168.1.11/](http://192.168.1.11/)

**在世界各地访问**
> ```bash
>  ip a | rg 'inet6 .* scope global dynamic'
>  # 输出类似 inet6 2409:8a1a:537:9850:7840:abff:fe1b:9266/64 scope global dynamic mngtmpaddr proto kernel_ra
>  ```
[http://[2409:8a1a:537:9850:7840:abff:fe1b:9266]/](http://[2409:8a1a:537:9850:7840:abff:fe1b:9266]/)

