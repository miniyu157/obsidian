### 安装

```bash
sudo pacman -S github-cli
```

### 登录

```bash
gh auth login
```

> 推荐选择 ssh

### 使用

查看自己的仓库列表

```bash
gh repo list
```

克隆某个仓库

```
gh repo clone 用户名/仓库
```

查看当前仓库信息

```bash
gh repo view
```

创建仓库并连接推送

```bash
gh repo create 仓库名称 --public --source=. --push
```

更改仓库描述

```bash
gh repo edit -d "新的描述"

# 如果不在那个目录
gh repo edit 用户名/仓库 -d "这是我的 Obsidian 新的描述"
```
