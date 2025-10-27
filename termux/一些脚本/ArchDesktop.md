创建文件

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```

添加执行权限

```bash
chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```

创建软连接，可以使用 `arch-desktop` 命令快捷启动

```bash
ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop ~/.local/bin/arch-desktop
```

脚本内容

```bash
#!/data/data/com.termux/files/usr/bin/sh

# 清除残留进程，打开显示
killall -9 termux-x11 2> /dev/null
termux-x11 :0 -dpi 96 &
am start --user 0 -n com.termux.x11/com.termux.x11.MainActivity

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
mkdir -p $ChrootPath/dev/shm
mkdir -p $ChrootPath/proc
mkdir -p $ChrootPath/sys
mkdir -p $ChrootPath/sdcard
mkdir -p /data/user/0/com.termux/files/usr/tmp && chmod 1777 /data/user/0/com.termux/files/usr/tmp

# 挂载文件系统
mount -t devpts devpts $ChrootPath/dev/pts
mount -t tmpfs -o size=512M tmpfs $ChrootPath/dev/shm
mount --bind /proc $ChrootPath/proc
mount --bind /sys $ChrootPath/sys
mount --bind /sdcard $ChrootPath/sdcard
# 挂载 Termux 的 tmp，使其 x11 能够通信
mount --bind /data/data/com.termux/files/usr/tmp $ChrootPath/tmp

# 等 X11 socket 就绪
while [ ! -S "/data/data/com.termux/files/usr/tmp/.X11-unix/X0" ]; do sleep 0.2; done

# 伪造 systemctl 和电源管理器
echo "#!/bin/sh
case \$1 in import-environment|list-jobs|start|stop|is-active|is-enabled) exit 0;; *) exec systemctl.orig \\\"\$@\\\" 2>/dev/null || exit 0;; esac" \
    > $ChrootPath/usr/bin/systemctl && chmod 755 $ChrootPath/usr/bin/systemctl
ln -sf /bin/true $ChrootPath/usr/bin/pm-is-supported

# Chroot 进入并启动桌面
exec chroot "$ChrootPath" /bin/su - yumeka -c "
mkdir -p /tmp/xdg-yumeka
chmod 0700 /tmp/xdg-yumeka
export DISPLAY=:0

# dbus-run-session startxfce4
dbus-launch --exit-with-session && TU_DEBUG=noconform ZINK_DESCRIPTORS=lazy ZINK_DEBUG=compact startxfce4
"

```

如果你很懒

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop ~/.local/bin/arch-desktop && echo 'IyEvZGF0YS9kYXRhL2NvbS50ZXJtdXgvZmlsZXMvdXNyL2Jpbi9zaAoKIyDmuIXpmaTmrovnlZnov5vnqIvvvIzmiZPlvIDmmL7npLoKa2lsbGFsbCAtOSB0ZXJtdXgteDExIDI+IC9kZXYvbnVsbAp0ZXJtdXgteDExIDowIC1kcGkgOTYgJgphbSBzdGFydCAtLXVzZXIgMCAtbiBjb20udGVybXV4LngxMS9jb20udGVybXV4LngxMS5NYWluQWN0aXZpdHkKCiMg5aaC5p6c5LiN5pivIHJvb3QsIOS9v+eUqCBzdSAtYyDph43mlrDmiafooYzohJrmnKzoh6rouqsKaWYgWyAiJChpZCAtdSkiIC1uZSAwIF07IHRoZW4KICAgICMgc2hlbGxjaGVjayBkaXNhYmxlPVNDMjAxNgogICAgZXhlYyBzdSAtYyAnZXhlYyAiJDAiICIkQCInICIkMCIgIiRAIgpmaQoKIyBDaHJvb3Qg6Lev5b6ECkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgoKIyDop6PlhrPnlKjmiLfliIfmjaLnmoQgc3VpZCDpl67popgKbW91bnQgLW8gcmVtb3VudCxkZXYsc3VpZCAvZGF0YQojIOaMgui9vSAvZGV2Cm1vdW50IC0tYmluZCAvZGV2ICRDaHJvb3RQYXRoL2RldgoKIyDliJvlu7rmjILovb3mlofku7bns7vnu5/miYDpnIDmlofku7blpLkKbWtkaXIgLXAgJENocm9vdFBhdGgvZGV2L3B0cwpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvc2htCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZApta2RpciAtcCAvZGF0YS91c2VyLzAvY29tLnRlcm11eC9maWxlcy91c3IvdG1wICYmIGNobW9kIDE3NzcgL2RhdGEvdXNlci8wL2NvbS50ZXJtdXgvZmlsZXMvdXNyL3RtcAoKIyDmjILovb3mlofku7bns7vnu58KbW91bnQgLXQgZGV2cHRzIGRldnB0cyAkQ2hyb290UGF0aC9kZXYvcHRzCm1vdW50IC10IHRtcGZzIC1vIHNpemU9NTEyTSB0bXBmcyAkQ2hyb290UGF0aC9kZXYvc2htCm1vdW50IC0tYmluZCAvcHJvYyAkQ2hyb290UGF0aC9wcm9jCm1vdW50IC0tYmluZCAvc3lzICRDaHJvb3RQYXRoL3N5cwptb3VudCAtLWJpbmQgL3NkY2FyZCAkQ2hyb290UGF0aC9zZGNhcmQKIyDmjILovb0gVGVybXV4IOeahCB0bXDvvIzkvb/lhbYgeDExIOiDveWkn+mAmuS/oQptb3VudCAtLWJpbmQgL2RhdGEvZGF0YS9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAgJENocm9vdFBhdGgvdG1wCgojIOetiSBYMTEgc29ja2V0IOWwsee7qgp3aGlsZSBbICEgLVMgIi9kYXRhL2RhdGEvY29tLnRlcm11eC9maWxlcy91c3IvdG1wLy5YMTEtdW5peC9YMCIgXTsgZG8gc2xlZXAgMC4yOyBkb25lCgojIOS8qumAoCBzeXN0ZW1jdGwg5ZKM55S15rqQ566h55CG5ZmoCmVjaG8gIiMhL2Jpbi9zaApjYXNlIFwkMSBpbiBpbXBvcnQtZW52aXJvbm1lbnR8bGlzdC1qb2JzfHN0YXJ0fHN0b3B8aXMtYWN0aXZlfGlzLWVuYWJsZWQpIGV4aXQgMDs7ICopIGV4ZWMgc3lzdGVtY3RsLm9yaWcgXFxcIlwkQFxcXCIgMj4vZGV2L251bGwgfHwgZXhpdCAwOzsgZXNhYyIgXAogICAgPiAkQ2hyb290UGF0aC91c3IvYmluL3N5c3RlbWN0bCAmJiBjaG1vZCA3NTUgJENocm9vdFBhdGgvdXNyL2Jpbi9zeXN0ZW1jdGwKbG4gLXNmIC9iaW4vdHJ1ZSAkQ2hyb290UGF0aC91c3IvYmluL3BtLWlzLXN1cHBvcnRlZAoKIyBDaHJvb3Qg6L+b5YWl5bm25ZCv5Yqo5qGM6Z2iCmV4ZWMgY2hyb290ICIkQ2hyb290UGF0aCIgL2Jpbi9zdSAtIHl1bWVrYSAtYyAiCm1rZGlyIC1wIC90bXAveGRnLXl1bWVrYQpjaG1vZCAwNzAwIC90bXAveGRnLXl1bWVrYQpleHBvcnQgRElTUExBWT06MAoKIyBkYnVzLXJ1bi1zZXNzaW9uIHN0YXJ0eGZjZTQKZGJ1cy1sYXVuY2ggLS1leGl0LXdpdGgtc2Vzc2lvbiAmJiBUVV9ERUJVRz1ub2NvbmZvcm0gWklOS19ERVNDUklQVE9SUz1sYXp5IFpJTktfREVCVUc9Y29tcGFjdCBzdGFydHhmY2U0CiIK' | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```
