#!/data/data/com.termux/files/usr/bin/sh

# 如果不是 root, 使用 su -c 重新执行脚本自身
if [ "$(id -u)" -ne 0 ]; then
    exec su -c 'exec "$0" "$@"' "$0" "$@"
fi

# Chroot 路径
ChrootPath="/data/local/tmp/arch"

# 取消挂载文件系统
umount -l $ChrootPath/sdcard
umount -l $ChrootPath/tmp
umount -l $ChrootPath/proc
umount -l $ChrootPath/sys
umount -l $ChrootPath/dev/shm
umount -l $ChrootPath/dev/pts
umount -l $ChrootPath/dev
