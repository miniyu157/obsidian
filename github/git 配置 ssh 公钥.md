1. 检验是否已经存在公钥
```bash
ls ~/.ssh/
```

2. 生成公钥
```bash
ssh-keygen -t ed25519 -C "miniyu157@163.com"
```

3. 查看公钥内容
```bash
cat ~/.ssh/id_ed25519.pub
```

4. 检验连接
```bash
ssh -T git@github.com
```

5. 前往 [github](https://github.com/settings/keys) 添加