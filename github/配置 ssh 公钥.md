1. 检验是否已经存在公钥
```bash
ls ~/.ssh/
```

2. 生成公钥
```bash
ssh-keygen -t ed25519 -C "miniyu157@163.com"
```

3. 查看公钥内容, 复制下来
```bash
cat ~/.ssh/id_ed25519.pub
```

4. 前往 [github](https://github.com/settings/keys) 添加

头像 -> Settings -> 侧边栏 SSH and GPG keys -> New SSh key

- Title: 起一个标题, 通常用于标识某个设备使用
- Key: 粘贴刚刚复制的公钥

Add SSH key: 确认添加

5. 检验连接
```bash
ssh -T git@github.com
```
