#!/bin/sh

su -c sh <<'EOF'
Source="/data/local/tmp/arch"
BackupDir="/storage/emulated/0/_Yumeka/linux/chroot备份"

ZstdPath="/data/data/com.termux/files/usr/bin/zstd"
PvPath="/data/data/com.termux/files/usr/bin/pv"
TarPath="/data/data/com.termux/files/usr/bin/tar"

mkdir -p "$BackupDir"

if [ -d "$Source" ]; then
  BackupFile="$BackupDir/arch-$(date +'[%y]%m.%d-%H:%M:%S').tar.zst"
  TotalSize=$(du -sb "$Source" | awk '{print $1}')
  "$TarPath" -cp -C "$(dirname "$Source")" "$(basename "$Source")" | "$PvPath" -s "$TotalSize" | "$ZstdPath" > "$BackupFile"
  echo "备份完成: $BackupFile"
else
  echo "错误: 源目录 '$Source' 不存在。"
fi
EOF