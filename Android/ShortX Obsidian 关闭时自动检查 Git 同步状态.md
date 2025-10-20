```json
{
  "facts": [{
    "@type": "type.googleapis.com/PkgStopRunning",
    "apps": [{
      "pkgName": "md.obsidian"
    }],
    "customContextDataKey": {
    },
    "id": "F-1eca7f92-df2c-44bd-b39a-19d2140931f2"
  }],
  "actions": [{
    "@type": "type.googleapis.com/ShellCommand",
    "command": "#!/system/bin/sh\n\nalias git\u003d/data/user/0/com.termux/files/usr/bin/git\n\nHOME\u003d/data/user/0/com.termux/files/home\nGITHUB_USER\u003d\"miniyu157\"\nREPO_PATH\u003d\"/storage/emulated/0/Documents/obsidian\"\nTOKEN_DB_PATH\u003d\u0027/data/user/0/md.obsidian/app_webview/Default/Local Storage/leveldb/\u0027\n# 从 obsidian 中提取令牌，如果设置了多次，修改此处 TOKEN_INDEX 的值\n# 或者取消注释下行代码直接列出令牌列表\n# echo $(strings \u0027/data/user/0/md.obsidian/app_webview/Default/Local Storage/leveldb/\u0027* | grep -A 1 \u0027obsidian-obsidian-git:password\u0027 | grep \u0027^\"\u0027 | sed \u0027s/\"\\([^\"]*\\).*/\\1/\u0027)\nTOKEN_INDEX\u003d5\n\n# 动态获取令牌\nGITHUB_TOKEN\u003d$(strings \"${TOKEN_DB_PATH}\"* | grep -A 1 \u0027obsidian-obsidian-git:password\u0027 | grep \u0027^\"\u0027 | sed \u0027s/\"\\([^\"]*\\).*/\\1/\u0027 | sed -n \"${TOKEN_INDEX}p\")\n\ncd \"$REPO_PATH\"\n\ngit remote set-url origin \"https://github.com/${GITHUB_USER}/obsidian.git\"\n\n# 使用凭据缓存\ngit config credential.helper \u0027cache --timeout\u003d300\u0027\n\n# 将令牌和用户名缓存\nprintf \"protocol\u003dhttps\\nhost\u003dgithub.com\\nusername\u003d%s\\npassword\u003d%s\\n\" \"$GITHUB_USER\" \"$GITHUB_TOKEN\" | git credential approve\n\n# 尝试拉取\ngit fetch --quiet 2\u003e/dev/null\nFETCH_STATUS\u003d$?\n\nif [ \"$FETCH_STATUS\" -ne 0 ]; then\n    echo \"❌  远程仓库拉取失败。请检查令牌或网络。\"\nfi\n\nBRANCH\u003d$(git rev-parse --abbrev-ref HEAD)\n\n# 检查工作区是否有更改\nUNSTAGED_COUNT\u003d$(git diff --name-only | wc -l | tr -d \u0027 \u0027)\nif [ \"$UNSTAGED_COUNT\" -gt 0 ]; then\n    echo \"⚠️  工作区有 $UNSTAGED_COUNT 个文件未提交。\"\nfi\n\n# 检查暂存区是否有更改\nSTAGED_COUNT\u003d$(git diff --cached --name-only | wc -l | tr -d \u0027 \u0027)\nif [ \"$STAGED_COUNT\" -gt 0 ]; then\n    echo \"⚠️  暂存区有 $STAGED_COUNT 个文件已暂存。\"\nfi\n\n# 只有在 fetch 成功时才检查未推送的提交\nif [ \"$FETCH_STATUS\" -eq 0 ]; then\n    UNPUSHED_COUNT\u003d$(git log origin/$BRANCH..HEAD --oneline | wc -l | tr -d \u0027 \u0027)\n    if [ \"$UNPUSHED_COUNT\" -gt 0 ]; then\n        echo \"⬆️  本地有 $UNPUSHED_COUNT 个未推送的提交。\"\n    fi\nfi\n\n# 检查是否有未跟踪的新文件\nUNTRACKED_COUNT\u003d$(git ls-files --others --exclude-standard | wc -l | tr -d \u0027 \u0027)\nif [ \"$UNTRACKED_COUNT\" -gt 0 ]; then\n    echo \"❓  有 $UNTRACKED_COUNT 个未跟踪的新文件。\"\nfi",
    "singleShot": true,
    "customContextDataKey": {
    },
    "id": "A-96565663-c25f-4484-9fd5-e0b82406bce7"
  }, {
    "@type": "type.googleapis.com/IfThenElse",
    "If": [{
      "@type": "type.googleapis.com/EvaluateContextVar",
      "op": "EqualTo",
      "varName": "shellOut",
      "payload": {
      },
      "customContextDataKey": {
      },
      "id": "C-4d0cf223-f695-4eb2-8a16-b46577673690"
    }],
    "ElseActions": [{
      "@type": "type.googleapis.com/ShowToast",
      "message": "{shellOut}",
      "customContextDataKey": {
      },
      "id": "A-9d46450b-78a8-4acc-aabe-5ca273d29994"
    }],
    "customContextDataKey": {
    },
    "id": "A-5d1f0936-835f-422b-bd64-b267e1450f89"
  }],
  "id": "SHARE-rule-365df782-beb5-4927-bfe0-096d00e1ca05",
  "lastUpdateTime": "1760962335393",
  "createTime": "1760951497290",
  "author": {
    "name": "ShortX"
  },
  "title": "Obsidian 关闭时自动检查 Git 同步状态",
  "description": "需要在 Termux 中安装 Git 并设置用户名、邮箱、添加安全文件夹；Obsidian 安装 Git 插件并设置令牌；没反应则授予 System UI Root 权限",
  "isEnabled": true,
  "hook": {
  },
  "quit": {
  },
  "versionCode": "1"
}
###------###
{"type":"rule"}
```

**原始脚本**

```bash
#!/system/bin/sh

alias git=/data/user/0/com.termux/files/usr/bin/git

HOME=/data/user/0/com.termux/files/home
GITHUB_USER="miniyu157"
REPO_PATH="/storage/emulated/0/Documents/obsidian"
TOKEN_DB_PATH='/data/user/0/md.obsidian/app_webview/Default/Local Storage/leveldb/'
# 从 obsidian 中提取令牌，如果设置了多次，修改此处 TOKEN_INDEX 的值
# 或者取消注释下行代码直接列出令牌列表
# echo $(strings '/data/user/0/md.obsidian/app_webview/Default/Local Storage/leveldb/'* | grep -A 1 'obsidian-obsidian-git:password' | grep '^"' | sed 's/"\([^"]*\).*/\1/')
TOKEN_INDEX=5

# 动态获取令牌
GITHUB_TOKEN=$(strings "${TOKEN_DB_PATH}"* | grep -A 1 'obsidian-obsidian-git:password' | grep '^"' | sed 's/"\([^"]*\).*/\1/' | sed -n "${TOKEN_INDEX}p")

cd "$REPO_PATH"

git remote set-url origin "https://github.com/${GITHUB_USER}/obsidian.git"

# 使用凭据缓存
git config credential.helper 'cache --timeout=300'

# 将令牌和用户名缓存
printf "protocol=https\nhost=github.com\nusername=%s\npassword=%s\n" "$GITHUB_USER" "$GITHUB_TOKEN" | git credential approve

# 尝试拉取
git fetch --quiet 2>/dev/null
FETCH_STATUS=$?

if [ "$FETCH_STATUS" -ne 0 ]; then
    echo "❌  远程仓库拉取失败。请检查令牌或网络。"
fi

BRANCH=$(git rev-parse --abbrev-ref HEAD)

# 检查工作区是否有更改
UNSTAGED_COUNT=$(git diff --name-only | wc -l | tr -d ' ')
if [ "$UNSTAGED_COUNT" -gt 0 ]; then
    echo "⚠️  工作区有 $UNSTAGED_COUNT 个文件未提交。"
fi

# 检查暂存区是否有更改
STAGED_COUNT=$(git diff --cached --name-only | wc -l | tr -d ' ')
if [ "$STAGED_COUNT" -gt 0 ]; then
    echo "⚠️  暂存区有 $STAGED_COUNT 个文件已暂存。"
fi

# 只有在 fetch 成功时才检查未推送的提交
if [ "$FETCH_STATUS" -eq 0 ]; then
    UNPUSHED_COUNT=$(git log origin/$BRANCH..HEAD --oneline | wc -l | tr -d ' ')
    if [ "$UNPUSHED_COUNT" -gt 0 ]; then
        echo "⬆️  本地有 $UNPUSHED_COUNT 个未推送的提交。"
    fi
fi

# 检查是否有未跟踪的新文件
UNTRACKED_COUNT=$(git ls-files --others --exclude-standard | wc -l | tr -d ' ')
if [ "$UNTRACKED_COUNT" -gt 0 ]; then
    echo "❓  有 $UNTRACKED_COUNT 个未跟踪的新文件。"
fi
```