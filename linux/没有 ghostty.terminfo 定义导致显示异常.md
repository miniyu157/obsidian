如果客户端使用了 ghostty, 服务器上没有安装, 通过 ssh 连接后会继承客户端的 $TERM, 此时需要将 ghostty.terminfo 安装到服务器才能正确解析

在客户端导出

```bash
infocmp > ~/xterm-ghostty.terminfo
```

上传到服务器

```bash
scp -P 37781 ~/xterm-ghostty.terminfo yumeka@server.yumeka.blog:~
```

### 登录服务器后安装

在所有用户中

```bash
sudo tic -x -o /usr/share/terminfo ~/xterm-ghostty.terminfo
```

仅限当前用户

```bash
tic -x ~/xterm-ghostty.terminfo
```

