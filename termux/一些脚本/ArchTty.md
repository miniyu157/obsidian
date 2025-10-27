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
exec chroot $ChrootPath /bin/su - $LoginUser

```

如果你很懒

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty ~/.local/bin/arch && echo "IyEvZGF0YS9kYXRhL2NvbS50ZXJtdXgvZmlsZXMvdXNyL2Jpbi9zaAoKIyDlpoLmnpzkuI3mmK8gcm9vdCwg5L2/55SoIHN1IC1jIOmHjeaWsOaJp+ihjOiEmuacrOiHqui6qwppZiBbICIkKGlkIC11KSIgLW5lIDAgXTsgdGhlbgogICAgZXhlYyBzdSAtYyAnZXhlYyAiJDAiICIkQCInICIkMCIgIiRAIgpmaQoKIyBDaHJvb3Qg6Lev5b6ECkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgoKIyDop6PlhrPnlKjmiLfliIfmjaLnmoQgc3VpZCDpl67popgKbW91bnQgLW8gcmVtb3VudCxkZXYsc3VpZCAvZGF0YQojIOaMgui9vSAvZGV2Cm1vdW50IC0tYmluZCAvZGV2ICRDaHJvb3RQYXRoL2RldgoKIyDliJvlu7rmjILovb3mlofku7bns7vnu5/miYDpnIDmlofku7blpLkKbWtkaXIgLXAgJENocm9vdFBhdGgvZGV2L3B0cwpta2RpciAtcCAkQ2hyb290UGF0aC9wcm9jCm1rZGlyIC1wICRDaHJvb3RQYXRoL3N5cwpta2RpciAtcCAkQ2hyb290UGF0aC9zZGNhcmQKCiMg5oyC6L295paH5Lu257O757ufCm1vdW50IC10IGRldnB0cyBkZXZwdHMgJENocm9vdFBhdGgvZGV2L3B0cwptb3VudCAtLWJpbmQgL3Byb2MgJENocm9vdFBhdGgvcHJvYwptb3VudCAtLWJpbmQgL3N5cyAkQ2hyb290UGF0aC9zeXMKbW91bnQgLS1iaW5kIC9zZGNhcmQgJENocm9vdFBhdGgvc2RjYXJkCgojIOeZu+W9leeUqOaIt+WSjOm7mOiupOWAvApMb2dpblVzZXI9IiR7MToteXVtZWthfSIKCiMgQ2hyb290IOi/m+WFpQpleGVjIGNocm9vdCAkQ2hyb290UGF0aCAvYmluL3N1IC0gJExvZ2luVXNlcgo=" | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty
```