创建公钥清单并修正权限

```bash
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

> 此时可以将授权的公钥追加到公钥清单中

编辑 sshd 配置文件

```bash
sudo nvim /etc/ssh/sshd_config
```

改成不常用端口号，禁用密码登录，强制使用公钥，禁用 root 登录

```
Port xxxxx
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
PermitRootLogin no
KbdInteractiveAuthentication no
```

终止 sshd（如果已经启动）

```bash
sudo kill -HUP $(sudo pidof sshd)
```

生成主机密钥

```bash
sudo ssh-keygen -A
```

启动 sshd

```bash
sudo /usr/bin/sshd
```

现在可以在其它持有公钥的设备上使用 ssh 登录服务器了