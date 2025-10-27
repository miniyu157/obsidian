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
