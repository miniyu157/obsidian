#### Android 服务器设置

添加防火墙规则，仅允许本地回环

```bash
sudo ip6tables -A INPUT -p tcp --dport 5555 ! -s ::1 -j DROP
```

验证

```bash
sudo ip6tables -L INPUT -n -v | rg 5555
```

启动 adbd 指定端口

```bash
setprop service.adb.tcp.port 5555 && stop adbd && start adbd
```

#### 客户端设置

开启 ssh 隧道，将本地端口映射到服务器的

```bash
ssh -N -L 5555:127.0.0.1:9876 -p 5555 yumeka@server.yumeka.blog
```

使用 adb 连接，此时应该在服务器点击授权

```bash
adb connect 127.0.0.1:5555
```

检验设备列表

```bash
adb devices
```

使用 scrcpy 连接

```bash
scrcpy --tcpip=127.0.0.1:5555
```

如果卡顿，可以自行调整参数

```bash
scrcpy \
  --tcpip=127.0.0.1:5555 \
  --max-size 1080 \
  --max-fps 60 \
  --video-codec=h265 \
  -b 16M \
  --print-fps \
  -f \
  --window-borderless \
  --no-mipmaps \
  --no-audio
```

或者调整加密算法

```bash
ssh -c chacha20-poly1305@openssh.com -L 5555:127.0.0.1:5555 yumeka@yumeka.blog -p 37781
```