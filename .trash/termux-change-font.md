```bash
#!/usr/bin/env bash

set_font_root() {
    local SourceFont="$1"
    local TargetDir="/data/user/0/com.termux/files/home/.termux"
    local TargetFont="$TargetDir/font.ttf"
    local TermuxDataDir="/data/data/com.termux/files/usr"

    local TermuxUID TermuxGID
    TermuxUID=$(stat -c "%u" "$TermuxDataDir") || return 1
    TermuxGID=$(stat -c "%g" "$TermuxDataDir") || return 1

    mkdir -p "$TargetDir"
    chown "$TermuxUID:$TermuxGID" "$TargetDir"
    cp -f "$SourceFont" "$TargetFont"
    chown "$TermuxUID:$TermuxGID" "$TargetFont"
    chmod 600 "$TargetFont"
}

if [ -z "$1" ]; then
    echo "用法: $0 <字体文件路径>"
    exit 1
fi

SourceFont="$1"
if [ ! -f "$SourceFont" ]; then
    echo "错误: 文件 '$SourceFont' 不存在。"
    exit 1
fi

export -f set_font_root
su -c "set_font_root '$SourceFont'"

echo "字体已成功设置为: $SourceFont"
echo "请重启 Termux 以应用更改。"
```
