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

如果你很懒


```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop ~/.local/bin/arch-desktop && echo "IyEvYmluL3NoCgpraWxsYWxsIC05IHRlcm11eC14MTEgMj4vZGV2L251bGwKdGVybXV4LXgxMSA6MCAtZHBpIDk2ICYKYW0gc3RhcnQgLS11c2VyIDAgLW4gY29tLnRlcm11eC54MTEvY29tLnRlcm11eC54MTEuTWFpbkFjdGl2aXR5CgpzdSAtYyAnCkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgpidXN5Ym94IG1vdW50IC1vIHJlbW91bnQsZGV2LHN1aWQgL2RhdGEKCiMg5L+u5aSNIC9kZXYvbnVsbCDpl67popgKaWYgWyAhIC1jIC9kZXYvbnVsbCBdOyB0aGVuCiAgICBybSAtZiAvZGV2L251bGwKICAgIG1rbm9kIC9kZXYvbnVsbCBjIDEgMwogICAgY2htb2QgNjY2IC9kZXYvbnVsbAogICAgY2hvd24gcm9vdDpyb290IC9kZXYvbnVsbApmaQpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvcHRzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZApta2RpciAtcCAkQ2hyb290UGF0aC90bXAKbWtkaXIgLXAgJENocm9vdFBhdGgvZGV2L3NobQoKbWtkaXIgLXAgL2RhdGEvdXNlci8wL2NvbS50ZXJtdXgvZmlsZXMvdXNyL3RtcApjaG1vZCAxNzc3IC9kYXRhL3VzZXIvMC9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAKCmJ1c3lib3ggbW91bnQgLS1iaW5kIC9kZXYgJENocm9vdFBhdGgvZGV2CmJ1c3lib3ggbW91bnQgLS1iaW5kIC9zeXMgJENocm9vdFBhdGgvc3lzCmJ1c3lib3ggbW91bnQgLS1iaW5kIC9wcm9jICRDaHJvb3RQYXRoL3Byb2MKYnVzeWJveCBtb3VudCAtdCBkZXZwdHMgZGV2cHRzICRDaHJvb3RQYXRoL2Rldi9wdHMKYnVzeWJveCBtb3VudCAtLWJpbmQgL3NkY2FyZCAkQ2hyb290UGF0aC9zZGNhcmQKYnVzeWJveCBtb3VudCAtLWJpbmQgL2RhdGEvZGF0YS9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAgJENocm9vdFBhdGgvdG1wCmJ1c3lib3ggbW91bnQgLXQgdG1wZnMgLW8gc2l6ZT01MTJNIHRtcGZzICRDaHJvb3RQYXRoL2Rldi9zaG0KCiMg562JIFgxMSBzb2NrZXQg5bCx57uqCndoaWxlIFsgISAtUyAiL2RhdGEvZGF0YS9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAvLlgxMS11bml4L1gwIiBdOyBkbyBzbGVlcCAwLjI7IGRvbmUKCiMg5Yqr5oyBIHN5c3RlbWN0bO+8jOiuqSBHTk9NRS9YZmNlIOe7hOS7tuS4jeWGjeaKpSBzdGF0dXMgMQplY2hvICIjIS9iaW4vc2gKY2FzZSBcJDEgaW4gaW1wb3J0LWVudmlyb25tZW50fGxpc3Qtam9ic3xzdGFydHxzdG9wfGlzLWFjdGl2ZXxpcy1lbmFibGVkKSBleGl0IDA7OyAqKSBleGVjIHN5c3RlbWN0bC5vcmlnIFxcXCJcJEBcXFwiIDI+L2Rldi9udWxsIHx8IGV4aXQgMDs7IGVzYWMiIFwKPiAkQ2hyb290UGF0aC91c3IvYmluL3N5c3RlbWN0bCAmJiBjaG1vZCA3NTUgJENocm9vdFBhdGgvdXNyL2Jpbi9zeXN0ZW1jdGwKCiMg6KGlIHBtLWlzLXN1cHBvcnRlZO+8jOmYsuatoiB4ZmNlNC1zZXNzaW9uIOaKpSLmsqHmnInpgqPkuKrmlofku7bmiJbnm67lvZUiCmxuIC1zZiAvYmluL3RydWUgJENocm9vdFBhdGgvdXNyL2Jpbi9wbS1pcy1zdXBwb3J0ZWQKCmV4ZWMgYnVzeWJveCBjaHJvb3QgIiRDaHJvb3RQYXRoIiAvYmluL3N1IC0geXVtZWthIC1jICIKbWtkaXIgLXAgL3RtcC94ZGcteXVtZWthCmNobW9kIDA3MDAgL3RtcC94ZGcteXVtZWthCmV4cG9ydCBESVNQTEFZPTowCmRidXMtcnVuLXNlc3Npb24gc3RhcnR4ZmNlNAoiCgonCg==" | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```