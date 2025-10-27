**安装 Certbot Nginx 插件**

Certbot 是 Let's Encrypt 的客户端，certbot-nginx 插件可以自动读取并修改 Nginx 配置

```bash
sudo pacman -S certbot certbot-nginx
```

**现在应该在路由器管理页面开放 443 端口**

编辑 `/etc/nginx/nginx.conf`，找到 server 块，将 server_name 从 _ 修改为域名，例如：

```
server {
    ...
    server_name  yumeka.blog www.yumeka.blog;
    ...
}
```

**运行 Certbot 获取并安装证书**

```bash
sudo certbot --nginx -d yumeka.blog -d www.yumeka.blog
```

> 此时会询问几个问题
> * 邮箱地址：用于接收续订提醒和安全通知
> * 同意服务条款：(Y)es
> * 分享你的邮箱：(N)o
> * 选择 HTTP -> HTTPS 重定向

**设置自动续订**

> 据说有效期只有 90 天

编辑定时任务列表

```bash
sudo EDITOR=nvim crontab -e
```

> 因为Certbot 需要在 /var/log/letsencrypt/ 目录中创建一个 .certbot.lock 锁文件，以防止多个实例同时运行，这需要 root 的权限，所以使用 sudo

```bash
0 4 * * * (date; /usr/bin/certbot renew --quiet) >> /var/log/certbot_renew.log 2>&1
```

> 它会每天凌晨四点运行 certbot renew 检查续订，只有在证书即将过期时才会真正执行续订
> 日志文件在 /var/log/certbot_renew.log

> [!NOTE] 测试一下
> ```bash
> sudo /usr/bin/certbot renew --dry-run
> ```
