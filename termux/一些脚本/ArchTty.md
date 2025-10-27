创建文件

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty
```

添加执行权限

```bash
chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty
```

创建软连接，可以使用 `arch` 命令快捷启动

```bash
ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty ~/.local/bin/arch
```

脚本内容

```bash
#!/data/data/com.termux/files/usr/bin/sh

# 如果不是 root, 使用 su -c 重新执行脚本自身
if [ "$(id -u)" -ne 0 ]; then
    # shellcheck disable=SC2016
    exec su -c 'exec "$0" "$@"' "$0" "$@"
fi

# Chroot 路径
ChrootPath="/data/local/tmp/arch"

# 解决用户切换的 suid 问题
mount -o remount,dev,suid /data
# 挂载 /dev
mount --bind /dev $ChrootPath/dev

# 创建挂载文件系统所需文件夹
mkdir -p $ChrootPath/dev/pts
mkdir -p $ChrootPath/proc
mkdir -p $ChrootPath/sys
mkdir -p $ChrootPath/sdcard

# 挂载文件系统
mount -t devpts devpts $ChrootPath/dev/pts
mount --bind /proc $ChrootPath/proc
mount --bind /sys $ChrootPath/sys
mount --bind /sdcard $ChrootPath/sdcard

# 登录用户和默认值
LoginUser="${1:-yumeka}"

# Chroot 进入
exec chroot $ChrootPath /bin/su - "$LoginUser"

```

如果你很懒

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty ~/.local/bin/arch && echo 'IyEvZGF0YS9kYXRhL2NvbS50ZXJtdXgvZmlsZXMvdXNyL2Jpbi9zaAoKIyDlpoLmnpzkuI3mmK8gcm9vdCwg5L2/55SoIHN1IC1jIOmHjeaWsOaJp+ihjOiEmuacrOiHqui6qwppZiBbICIkKGlkIC11KSIgLW5lIDAgXTsgdGhlbgogICAgIyBzaGVsbGNoZWNrIGRpc2FibGU9U0MyMDE2CiAgICBleGVjIHN1IC1jICdleGVjICIkMCIgIiRAIicgIiQwIiAiJEAiCmZpCgojIENocm9vdCDot6/lvoQKQ2hyb290UGF0aD0iL2RhdGEvbG9jYWwvdG1wL2FyY2giCgojIOino+WGs+eUqOaIt+WIh+aNoueahCBzdWlkIOmXrumimAptb3VudCAtbyByZW1vdW50LGRldixzdWlkIC9kYXRhCiMg5oyC6L29IC9kZXYKbW91bnQgLS1iaW5kIC9kZXYgJENocm9vdFBhdGgvZGV2CgojIOWIm+W7uuaMgui9veaWh+S7tuezu+e7n+aJgOmcgOaWh+S7tuWkuQpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvcHRzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZAoKIyDmjILovb3mlofku7bns7vnu58KbW91bnQgLXQgZGV2cHRzIGRldnB0cyAkQ2hyb290UGF0aC9kZXYvcHRzCm1vdW50IC0tYmluZCAvcHJvYyAkQ2hyb290UGF0aC9wcm9jCm1vdW50IC0tYmluZCAvc3lzICRDaHJvb3RQYXRoL3N5cwptb3VudCAtLWJpbmQgL3NkY2FyZCAkQ2hyb290UGF0aC9zZGNhcmQKCiMg55m75b2V55So5oi35ZKM6buY6K6k5YC8CkxvZ2luVXNlcj0iJHsxOi15dW1la2F9IgoKIyBDaHJvb3Qg6L+b5YWlCmV4ZWMgY2hyb290ICRDaHJvb3RQYXRoIC9iaW4vc3UgLSAiJExvZ2luVXNlciIK' | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty
```
