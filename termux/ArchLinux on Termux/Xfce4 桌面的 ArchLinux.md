> [BusyBox](https://github.com/Magisk-Modules-Alt-Repo/BuiltIn-BusyBox/releases) 面具模块，使用 busybox 挂载

## Termux 环节

```
pkg install tsu wget
```

```bash
tsu
```

```bash
cd /data/local/tmp/
```

```bash
mkdir arch && wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz && tar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C arch/ && rm arch/etc/resolv.conf && rm ArchLinuxARM-aarch64-latest.tar.gz
```

## Arch 环节

**启动脚本**

[[ArchTty]]

**设置网络**

```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

```bash
echo "127.0.0.1 localhost" > /etc/hosts
```

**设置软件源和 pacman**

```bash
sed -i.bak -e '/^## Geo-IP based mirror selection and load balancing/i\Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/$arch/$repo\n' -e 's|^Server = http://mirror.archlinuxarm.org/\$arch/\$repo|# &|' /etc/pacman.d/mirrorlist
```

```bash
sed -i.bak '1i TMPDIR=/var/tmp' /etc/mkinitcpio.conf
```

```bash
sed -i.bak -e 's/^#Color/Color/' -e '/^CheckSpace/s/^/#/' /etc/pacman.conf
```

```bash
pacman-key --init
```

```bash
pacman-key --populate archlinuxarm
```

```bash
pacman -Syu
```

**安装软件包**

必要的

```bash
pacman -S --noconfirm --needed sudo git wget curl openssh vim base-devel unzip p7zip
```

常用工具

```bash
pacman -S --noconfirm --needed neovim micro rsync fzf zellij fd eza ripgrep zoxide atuin bat btop fastfetch gdu duf tealdeer inxi devtools
```

其他 shell（或者以后更换）

* [[fish]]
* [[zsh]]

**用户和本地化**

```bash
passwd
```

```bash
useradd -m -G wheel -s /bin/bash yumeka
```

```bash
passwd yumeka
```

```bash
sed -i.bak 's/^# *%wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers
```

```bash
sed -i.bak -e '/^# *en_US.UTF-8 UTF-8/s/^# *//' -e '/^# *zh_CN.UTF-8 UTF-8/s/^# *//' /etc/locale.gen
```

```bash
locale-gen
```

```bash
echo "LANG=zh_CN.UTF-8" > /etc/locale.conf
```

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

**修复 fakeroot**

安装 fakeroot-tcp

```bash
wget "https://raw.githubusercontent.com/miniyu157/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz/refs/heads/main/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz" && pacman -U fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz && rm fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz
```

如果链接寄了

```bash
git clone https://aur.archlinux.org/fakeroot-tcp.git
cd fakeroot-tcp/
makepkg

cd ~/fakeroot-tcp
mkdir -p /tmp/fakeroot-install # 创建一个临时安装目录
cd src/fakeroot-1.37.1.2 # 进入编译好的源码目录
make DESTDIR=/tmp/fakeroot-install install # 手动安装到临时目录

# 备份系统原有的 fakeroot 和 faked
sudo mv /usr/bin/fakeroot /usr/bin/fakeroot.bak
sudo mv /usr/bin/faked /usr/bin/faked.bak

# 创建软链接，将 fakeroot 和 faked 指向临时的 fakeroot-tcp
sudo ln -s /tmp/fakeroot-install/usr/bin/fakeroot /usr/bin/fakeroot
sudo ln -s /tmp/fakeroot-install/usr/bin/faked /usr/bin/faked

# 验证临时的 fakeroot-tcp 是否能工作
fakeroot --version

# 生成标准 pacman 软件包
cd ~/fakeroot-tcp
makepkg

# 撤销劫持
sudo rm /usr/bin/fakeroot
sudo rm /usr/bin/faked
sudo mv /usr/bin/fakeroot.bak /usr/bin/fakeroot
sudo mv /usr/bin/faked.bak /usr/bin/faked

# 进行标准安装
sudo pacman -U fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz

# 清理临时文件
sudo rm -r /tmp/fakeroot-install
cd ~
sudo rm -r ~/fakeroot-tcp
```

**安装 aur 助手**

先切换用户

```bash
su - yumeka
```

* 如果你想用 yay
	```bash
	git clone https://aur.archlinux.org/yay.git && cd yay/ && makepkg -si && cd .. && rm -rf yay/
	```

* 如果你想用 paru
	```bash
	git clone https://aur.archlinux.org/paru.git && cd paru/ && makepkg -si && cd .. && rm -rf paru/
	```

> [!NOTE]
> 此时可以备份系统，以后搞坏系统可以更方便的恢复
>  
[[backup-arch.sh]] [[restore-arch.sh]]

**安装桌面**

xfce4

```bash
sudo pacman -S --noconfirm xfce4 xfce4-goodies
```

字体

```bash
sudo pacman -S --noconfirm --needed ttf-fira-code wqy-microhei noto-fonts-emoji
```

略缩图插件

```bash
sudo pacman -S --noconfirm --needed tumbler ffmpegthumbnailer poppler-glib libgsf libopenraw gstreamer gst-libav libgepub
```

回收站

```bash
sudo pacman -S gvfs
```

装好后 exit 退出来

```bash
pkg install -y x11-repo && pkg install -y termux-x11
```

启动脚本

[[ArchDesktop]]