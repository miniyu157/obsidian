> [Termux](https://github.com/termux/termux-app/releases) termux 本体，建议保活
> [termux-x11](https://github.com/termux/termux-x11/releases) 显示器
> [termux-widget](https://github.com/termux/termux-widget/releases) 把脚本放在桌面上
> [termux-styling](https://github.com/termux/termux-styling/releases) 给termux 换个好看的字体和主题

```bash
termux-change-repo
termux-setup-storage # 可能仍然需要进入系统设置授予权限
```

```bash
pkg update && pkg upgrade -y
```

基础

```bash
pkg install -y tsu wget curl git which openssh eza ripgrep fd bat zoxide atuin vim neovim fastfetch gdu inxi
```

进阶

```bash
pkg install -y pv gh
```

添加常用路径存放脚本

```bash
mkdir -p /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/
mkdir -p /data/user/0/com.termux/files/home/.local/bin
```

> 配置其他 shell

> [!NOTE]
> [[fish]] [[zsh]]
