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