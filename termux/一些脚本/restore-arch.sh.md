#!/bin/sh

su -c sh <<'EOF'
BackupDir="/storage/emulated/0/_Yumeka/linux/chroot备份"
RestoreDir="/data/local/tmp"
TargetDir="$RestoreDir/arch"

ZstdPath="/data/data/com.termux/files/usr/bin/zstd"
PvPath="/data/data/com.termux/files/usr/bin/pv"
TarPath="/data/data/com.termux/files/usr/bin/tar"

cd "$BackupDir" || { echo "错误: 无法访问备份目录 '$BackupDir'"; exit 1; }
set -- arch-*.tar.zst

if [ "$#" -eq 0 ] || [ ! -f "$1" ]; then
    echo "在 '$BackupDir' 中未找到任何备份文件。"
    exit 0
fi

echo "找到以下备份文件 (按时间从旧到新):"
i=1
for file in "$@"; do
    echo "  $i) $file"
    i=$((i + 1))
done
echo

printf "请输入要恢复的备份编号 (输入其他内容则取消): "
read -r choice </dev/tty

if ! [ "$choice" -ge 1 ] 2>/dev/null || ! [ "$choice" -le "$#" ] 2>/dev/null; then
    echo "操作已取消。"
    exit 0
fi

eval "SelectedFile=\${$choice}"

if [ -d "$TargetDir" ]; then
  echo "错误: 恢复目标 '$TargetDir' 已存在。"
  exit 1
fi

echo "准备从 '$SelectedFile' 恢复到 '$RestoreDir'..."
"$PvPath" "$SelectedFile" | "$ZstdPath" -d | "$TarPath" -xp -C "$RestoreDir"

if [ -d "$TargetDir" ]; then
    echo "恢复成功: '$TargetDir' 已创建。"
else
    echo "错误: 恢复失败。"
    exit 1
fi
EOF