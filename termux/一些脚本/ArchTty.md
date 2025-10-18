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
#!/bin/sh

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

busybox mount --bind /dev $ChrootPath/dev
busybox mount --bind /sys $ChrootPath/sys
busybox mount --bind /proc $ChrootPath/proc
busybox mount -t devpts devpts $ChrootPath/dev/pts
busybox mount --bind /sdcard $ChrootPath/sdcard

exec busybox chroot $ChrootPath /bin/su - root
'
```

如果你很懒

```bash
touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty ~/.local/bin/arch && echo "IyEvYmluL3NoCgpzdSAtYyAnCkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgpidXN5Ym94IG1vdW50IC1vIHJlbW91bnQsZGV2LHN1aWQgL2RhdGEKCiMg5L+u5aSNIC9kZXYvbnVsbCDpl67popgKaWYgWyAhIC1jIC9kZXYvbnVsbCBdOyB0aGVuCiAgICBybSAtZiAvZGV2L251bGwKICAgIG1rbm9kIC9kZXYvbnVsbCBjIDEgMwogICAgY2htb2QgNjY2IC9kZXYvbnVsbAogICAgY2hvd24gcm9vdDpyb290IC9kZXYvbnVsbApmaQpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvcHRzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZAoKYnVzeWJveCBtb3VudCAtLWJpbmQgL2RldiAkQ2hyb290UGF0aC9kZXYKYnVzeWJveCBtb3VudCAtLWJpbmQgL3N5cyAkQ2hyb290UGF0aC9zeXMKYnVzeWJveCBtb3VudCAtLWJpbmQgL3Byb2MgJENocm9vdFBhdGgvcHJvYwpidXN5Ym94IG1vdW50IC10IGRldnB0cyBkZXZwdHMgJENocm9vdFBhdGgvZGV2L3B0cwpidXN5Ym94IG1vdW50IC0tYmluZCAvc2RjYXJkICRDaHJvb3RQYXRoL3NkY2FyZAoKZXhlYyBidXN5Ym94IGNocm9vdCAkQ2hyb290UGF0aCAvYmluL3N1IC0gcm9vdAon" | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty
```