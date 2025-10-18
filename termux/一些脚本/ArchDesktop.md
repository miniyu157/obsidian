创建文件

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```

添加执行权限

```bash
chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```

创建软连接，可以使用 `arch` 命令快捷启动

```bash
ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop ~/.local/bin/arch-desktop
```

脚本内容

```bash
#!/bin/sh

killall -9 termux-x11 2>/dev/null
termux-x11 :0 -dpi 96 &
am start --user 0 -n com.termux.x11/com.termux.x11.MainActivity

su -c '
ChrootPath="/data/local/tmp/arch"
busybox mount -o remount,dev,suid /data

# 修复 /dev/null 问题
if [ ! -c /dev/null ]; then
    rm -f /dev/null
    mknod /dev/null c 1 3
    chmod 666 /dev/null
    chown root:root /dev/null
fi
mkdir -p $ChrootPath/dev/pts
mkdir -p $ChrootPath/proc
mkdir -p $ChrootPath/sys
mkdir -p $ChrootPath/sdcard
mkdir -p $ChrootPath/tmp
mkdir -p $ChrootPath/dev/shm

mkdir -p /data/user/0/com.termux/files/usr/tmp
chmod 1777 /data/user/0/com.termux/files/usr/tmp

busybox mount --bind /dev $ChrootPath/dev
busybox mount --bind /sys $ChrootPath/sys
busybox mount --bind /proc $ChrootPath/proc
busybox mount -t devpts devpts $ChrootPath/dev/pts
busybox mount --bind /sdcard $ChrootPath/sdcard
busybox mount --bind /data/data/com.termux/files/usr/tmp $ChrootPath/tmp
busybox mount -t tmpfs -o size=512M tmpfs $ChrootPath/dev/shm

# 等 X11 socket 就绪
while [ ! -S "/data/data/com.termux/files/usr/tmp/.X11-unix/X0" ]; do sleep 0.2; done

# 劫持 systemctl，让 GNOME/Xfce 组件不再报 status 1
echo "#!/bin/sh
case \$1 in import-environment|list-jobs|start|stop|is-active|is-enabled) exit 0;; *) exec systemctl.orig \\\"\$@\\\" 2>/dev/null || exit 0;; esac" \
> $ChrootPath/usr/bin/systemctl && chmod 755 $ChrootPath/usr/bin/systemctl

# 补 pm-is-supported，防止 xfce4-session 报"没有那个文件或目录"
ln -sf /bin/true $ChrootPath/usr/bin/pm-is-supported

exec busybox chroot "$ChrootPath" /bin/su - yumeka -c "
mkdir -p /tmp/xdg-yumeka
chmod 0700 /tmp/xdg-yumeka
export DISPLAY=:0
dbus-run-session startxfce4
"

'

```