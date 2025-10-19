> [BusyBox](https://github.com/Magisk-Modules-Alt-Repo/BuiltIn-BusyBox/releases) 面具模块，使用 busybox 挂载

### termux 环节

```
pkg install tsu wget
```

```bash
tsu
```

```bash
cd /data/local/tmp/
```

下载最新镜像

```bash
mkdir arch && wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz && tar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C arch/ && rm arch/etc/resolv.conf && rm ArchLinuxARM-aarch64-latest.tar.gz
```

### 进入 archlinux

![[ArchTty]]

### 设置网络

```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

```bash
echo "127.0.0.1 localhost" > /etc/hosts
```

### 设置软件源和 pacman

编辑 `/data/local/tmp/arch/etc/pacman.d/mirrorlist`

```bash
nano /etc/pacman.d/mirrorlist
```

> 添加
> ```
> Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/$arch/$repo
> ```
> 同时可以注释掉其他源

> 如果你很懒

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

### 安装软件包

```bash
pacman -Syu
```

必要的

```bash
pacman -S --noconfirm --needed sudo git wget curl openssh vim base-devel unzip p7zip
```

常用工具

```bash
pacman -S --noconfirm --needed neovim micro rsync fzf zellij fd eza ripgrep zoxide atuin bat btop fastfetch gdu duf tealdeer inxi
```

其他 shell（或者进入用户后更换）

```bash
# 如果你想用 fish
pacman -S fish
# 如果你你想用 zsh
pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting zsh-completions
```

### 用户和本地化

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

### 修复 fakeroot

安装 fakeroot-tcp

```bash
wget "https://raw.githubusercontent.com/miniyu157/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz/refs/heads/main/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz"
pacman -U fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz
rm fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz
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

### 安装 aur 助手

```bash
su - yumeka
```

> 如果你想用 yay

```bash
git clone https://aur.archlinux.org/yay.git && cd yay/ && makepkg -si && cd .. && rm -rf yay/
```

> 如果你想用 paru

```bash
git clone https://aur.archlinux.org/paru.git && cd paru/ && makepkg -si && cd .. && rm -rf paru/
```

> [!NOTE]
> 此时可以备份系统，以后搞坏系统可以更方便的恢复
>  [转到](其他问题.md#备份系统)

### 安装桌面

如果你想安装最小化桌面懒得折腾，以下是不包含音频模块，硬件加速的流程

```bash
sudo pacman -S --noconfirm xfce4 xfce4-goodies
```

主题（编译很慢）

```bash
paru -S yaru-xfwm4-theme yaru-gtk-theme yaru-icon-theme
```

字体

```bash
pacman -S --noconfirm --needed ttf-fira-code wqy-microhei noto-fonts-emoji
```

略缩图插件

```bash
sudo pacman -S --noconfirm --needed tumbler ffmpegthumbnailer poppler-glib libgsf libopenraw gstreamer gst-libav libgepub
```

装好后 exit 退出来

```bash
pkg install -y x11-repo && pkg install -y termux-x11
```

![[ArchDesktop]]

### 安装桌面（带硬件加速）

> 安装驱动

[mesa-turnip-kgsl-24.1.0-devel](https://github.com/LinuxDroidMaster/Termux-Desktops/blob/main/Documentation/HardwareAcceleration.md) 的驱动

```bash
sudo rm -f /usr/lib/libvulkan_freedreno.so /usr/share/vulkan/icd.d/freedreno_icd.aarch64.json && sudo pacman -S --noconfirm --needed unzip mesa vulkan-icd-loader vulkan-headers && wget https://github.com/MatrixhKa/mesa-turnip/releases/download/24.1.0/mesa-turnip-kgsl-24.1.0-devel.zip && unzip mesa-* -d turnip/ && cd turnip/ && sudo mkdir -p /usr/share/vulkan/icd.d/ && sudo mv libvulkan_freedreno.so /usr/lib/ && sudo mv freedreno_icd.aarch64.json /usr/share/vulkan/icd.d/ && cd .. && rm -r turnip/ mesa-turnip-kgsl-24.1.0-devel.zip
```

[mesa-vulkan-kgsl_24.1.0-devel-20240324_arm64.deb](https://www.bilibili.com/opus/1042342069764358179) 的驱动，确保 mesa-vulkan-kgsl_24.1.0-devel-20240324_arm64.deb 已经位于主目录

```bash
sudo rm -f /usr/lib/libvulkan_freedreno.so /usr/share/vulkan/icd.d/freedreno_icd.aarch64.json && sudo pacman -S --noconfirm --needed mesa vulkan-icd-loader vulkan-headers && mkdir -p ~/turnip_temp && cp ~/mesa-vulkan-kgsl_24.1.0-devel-20240324_arm64.deb ~/turnip_temp/ && cd ~/turnip_temp && ar x mesa-vulkan-kgsl_24.1.0-devel-20240324_arm64.deb && tar -xf data.tar.zst && sudo mv usr/lib/aarch64-linux-gnu/libvulkan_freedreno.so /usr/lib/ && sudo mv usr/share/vulkan/icd.d/freedreno_icd.aarch64.json /usr/share/vulkan/icd.d/ && cd ~ && rm -rf ~/turnip_temp && sudo sed -i 's|/usr/lib/aarch64-linux-gnu/|/usr/lib/|g' /usr/share/vulkan/icd.d/freedreno_icd.aarch64.json && rm ~/mesa-vulkan-kgsl_24.1.0-devel-20240324_arm64.deb
```

先卸载 vulkan-icd-loader 防止黑屏

```bash
sudo pacman -R vulkan-icd-loader
```

> [!NOTE]
> 新建 [启动桌面脚本](其他问题.md#TurnipXfce4启动桌面脚本) 并启动桌面，然后在设置中禁用混成器

重新安装 vulkan-icd-loader

```bash
sudo pacman -S vulkan-icd-loader
```

> 验证

```bash
sudo pacman -S --noconfirm --needed mesa-utils
glxinfo | grep "OpenGL renderer"
glxgears

# 可选的
sudo pacman -S --noconfirm --needed vulkan-tools
vkcube # 由于兼容性问题，vkcube 可能不工作
```