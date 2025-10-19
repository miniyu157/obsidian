> [BusyBox](https://github.com/Magisk-Modules-Alt-Repo/BuiltIn-BusyBox/releases) 面具模块，使用 busybox 挂载

### termux 环节

```bash
termux-change-repo && termux-setup-storage && mkdir -p /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ && mkdir -p /data/user/0/com.termux/files/home/.local/bin && pkg update && pkg upgrade -y && pkg install -y wget && su -c "mkdir /data/local/tmp/arch && /data/data/com.termux/files/usr/bin/wget -O /data/local/tmp/ArchLinuxARM-aarch64-latest.tar.gz http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz && /data/data/com.termux/files/usr/bin/tar -xpf /data/local/tmp/ArchLinuxARM-aarch64-latest.tar.gz -C /data/local/tmp/arch/ && rm /data/local/tmp/arch/etc/resolv.conf && rm /data/local/tmp/ArchLinuxARM-aarch64-latest.tar.gz" && touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty /data/user/0/com.termux/files/home/.local/bin/arch && echo "IyEvYmluL3NoCgpzdSAtYyAnCkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgpidXN5Ym94IG1vdW50IC1vIHJlbW91bnQsZGV2LHN1aWQgL2RhdGEKCiMg5L+u5aSNIC9kZXYvbnVsbCDpl67popgKaWYgWyAhIC1jIC9kZXYvbnVsbCBdOyB0aGVuCiAgICBybSAtZiAvZGV2L251bGwKICAgIG1rbm9kIC9kZXYvbnVsbCBjIDEgMwogICAgY2htb2QgNjY2IC9kZXYvbnVsbAogICAgY2hvd24gcm9vdDpyb290IC9kZXYvbnVsbApmaQpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvcHRzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZAoKYnVzeWJveCBtb3VudCAtLWJpbmQgL2RldiAkQ2hyb290UGF0aC9kZXYKYnVzeWJveCBtb3VudCAtLWJpbmQgL3N5cyAkQ2hyb290UGF0aC9zeXMKYnVzeWJveCBtb3VudCAtLWJpbmQgL3Byb2MgJENocm9vdFBhdGgvcHJvYwpidXN5Ym94IG1vdW50IC10IGRldnB0cyBkZXZwdHMgJENocm9vdFBhdGgvZGV2L3B0cwpidXN5Ym94IG1vdW50IC0tYmluZCAvc2RjYXJkICRDaHJvb3RQYXRoL3NkY2FyZAoKZXhlYyBidXN5Ym94IGNocm9vdCAkQ2hyb290UGF0aCAvYmluL3N1IC0gcm9vdAon" | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchTty && /data/user/0/com.termux/files/home/.local/bin/arch
```

**做了什么？**
- 初始化 Termux 并安装 wget
- 安装 ArchLinuxArm 到 `/data/local/tmp/arch/`
- 创建启动脚本到 `/data/user/0/com.termux/files/home/.local/bin/arch`

### 设置网络、软件源、用户组、本地化

其中需要输入四次密码，前两次是 root，后两次是创建的用户

```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf && echo "127.0.0.1 localhost" > /etc/hosts && sed -i.bak -e '/^## Geo-IP based mirror selection and load balancing/i\Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/$arch/$repo\n' -e 's|^Server = http://mirror.archlinuxarm.org/\$arch/\$repo|# &|' /etc/pacman.d/mirrorlist && sed -i.bak '1i TMPDIR=/var/tmp' /etc/mkinitcpio.conf && sed -i.bak -e 's/^#Color/Color/' -e '/^CheckSpace/s/^/#/' /etc/pacman.conf && pacman-key --init && pacman-key --populate archlinuxarm && pacman -Syu && pacman -S --noconfirm --needed sudo git wget curl openssh vim base-devel unzip p7zip && pacman -S --noconfirm --needed neovim micro rsync fzf zellij fd eza ripgrep zoxide atuin bat btop fastfetch gdu duf tealdeer inxi && passwd && useradd -m -G wheel -s /bin/bash yumeka && passwd yumeka && sed -i.bak 's/^# *%wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers && sed -i.bak -e '/^# *en_US.UTF-8 UTF-8/s/^# *//' -e '/^# *zh_CN.UTF-8 UTF-8/s/^# *//' /etc/locale.gen && locale-gen && echo "LANG=zh_CN.UTF-8" > /etc/locale.conf && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

**做了什么？**
- 设置 dns 和 hosts
- pacman 更换清华源
- 初始化 pacman
- 安装了基础软件包
- 安装并设置为中文 locale
- 设置时区为上海

### 修复 fakeroot

安装 fakeroot-tcp

```bash
wget "https://raw.githubusercontent.com/miniyu157/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz/refs/heads/main/fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz" && pacman -U fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz && rm fakeroot-tcp-1.37.1.2-1-aarch64.pkg.tar.xz
```

这样就可以使用 makepkg 了

### 安装 aur 助手

```bash
su - yumeka
```

```bash
git clone https://aur.archlinux.org/paru.git && cd paru/ && makepkg -si && cd .. && rm -rf paru/
```

> [!NOTE]
> 至此你获得了一个命令行版的 ArchLinux

### 安装桌面

安装 xfce4 桌面，等宽、中文、emoji字体，略缩图插件，回收站

```bash
sudo pacman -S xfce4 xfce4-goodies && sudo pacman -S ttf-fira-code wqy-microhei noto-fonts-emoji && sudo pacman -S --noconfirm --needed tumbler ffmpegthumbnailer poppler-glib libgsf libopenraw gstreamer gst-libav libgepub gvfs
```

装好后 exit 两次退出到 termux，以下命令安装 termux-x11 并创建启动脚本 

```bash
pkg install -y x11-repo && pkg install -y termux-x11 && touch /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && chmod +x /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop && ln -s /data/data/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop ~/.local/bin/arch-desktop && echo "IyEvYmluL3NoCgpraWxsYWxsIC05IHRlcm11eC14MTEgMj4vZGV2L251bGwKdGVybXV4LXgxMSA6MCAtZHBpIDk2ICYKYW0gc3RhcnQgLS11c2VyIDAgLW4gY29tLnRlcm11eC54MTEvY29tLnRlcm11eC54MTEuTWFpbkFjdGl2aXR5CgpzdSAtYyAnCkNocm9vdFBhdGg9Ii9kYXRhL2xvY2FsL3RtcC9hcmNoIgpidXN5Ym94IG1vdW50IC1vIHJlbW91bnQsZGV2LHN1aWQgL2RhdGEKCiMg5L+u5aSNIC9kZXYvbnVsbCDpl67popgKaWYgWyAhIC1jIC9kZXYvbnVsbCBdOyB0aGVuCiAgICBybSAtZiAvZGV2L251bGwKICAgIG1rbm9kIC9kZXYvbnVsbCBjIDEgMwogICAgY2htb2QgNjY2IC9kZXYvbnVsbAogICAgY2hvd24gcm9vdDpyb290IC9kZXYvbnVsbApmaQpta2RpciAtcCAkQ2hyb290UGF0aC9kZXYvcHRzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3Byb2MKbWtkaXIgLXAgJENocm9vdFBhdGgvc3lzCm1rZGlyIC1wICRDaHJvb3RQYXRoL3NkY2FyZApta2RpciAtcCAkQ2hyb290UGF0aC90bXAKbWtkaXIgLXAgJENocm9vdFBhdGgvZGV2L3NobQoKbWtkaXIgLXAgL2RhdGEvdXNlci8wL2NvbS50ZXJtdXgvZmlsZXMvdXNyL3RtcApjaG1vZCAxNzc3IC9kYXRhL3VzZXIvMC9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAKCmJ1c3lib3ggbW91bnQgLS1iaW5kIC9kZXYgJENocm9vdFBhdGgvZGV2CmJ1c3lib3ggbW91bnQgLS1iaW5kIC9zeXMgJENocm9vdFBhdGgvc3lzCmJ1c3lib3ggbW91bnQgLS1iaW5kIC9wcm9jICRDaHJvb3RQYXRoL3Byb2MKYnVzeWJveCBtb3VudCAtdCBkZXZwdHMgZGV2cHRzICRDaHJvb3RQYXRoL2Rldi9wdHMKYnVzeWJveCBtb3VudCAtLWJpbmQgL3NkY2FyZCAkQ2hyb290UGF0aC9zZGNhcmQKYnVzeWJveCBtb3VudCAtLWJpbmQgL2RhdGEvZGF0YS9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAgJENocm9vdFBhdGgvdG1wCmJ1c3lib3ggbW91bnQgLXQgdG1wZnMgLW8gc2l6ZT01MTJNIHRtcGZzICRDaHJvb3RQYXRoL2Rldi9zaG0KCiMg562JIFgxMSBzb2NrZXQg5bCx57uqCndoaWxlIFsgISAtUyAiL2RhdGEvZGF0YS9jb20udGVybXV4L2ZpbGVzL3Vzci90bXAvLlgxMS11bml4L1gwIiBdOyBkbyBzbGVlcCAwLjI7IGRvbmUKCiMg5Yqr5oyBIHN5c3RlbWN0bO+8jOiuqSBHTk9NRS9YZmNlIOe7hOS7tuS4jeWGjeaKpSBzdGF0dXMgMQplY2hvICIjIS9iaW4vc2gKY2FzZSBcJDEgaW4gaW1wb3J0LWVudmlyb25tZW50fGxpc3Qtam9ic3xzdGFydHxzdG9wfGlzLWFjdGl2ZXxpcy1lbmFibGVkKSBleGl0IDA7OyAqKSBleGVjIHN5c3RlbWN0bC5vcmlnIFxcXCJcJEBcXFwiIDI+L2Rldi9udWxsIHx8IGV4aXQgMDs7IGVzYWMiIFwKPiAkQ2hyb290UGF0aC91c3IvYmluL3N5c3RlbWN0bCAmJiBjaG1vZCA3NTUgJENocm9vdFBhdGgvdXNyL2Jpbi9zeXN0ZW1jdGwKCiMg6KGlIHBtLWlzLXN1cHBvcnRlZO+8jOmYsuatoiB4ZmNlNC1zZXNzaW9uIOaKpSLmsqHmnInpgqPkuKrmlofku7bmiJbnm67lvZUiCmxuIC1zZiAvYmluL3RydWUgJENocm9vdFBhdGgvdXNyL2Jpbi9wbS1pcy1zdXBwb3J0ZWQKCmV4ZWMgYnVzeWJveCBjaHJvb3QgIiRDaHJvb3RQYXRoIiAvYmluL3N1IC0geXVtZWthIC1jICIKbWtkaXIgLXAgL3RtcC94ZGcteXVtZWthCmNobW9kIDA3MDAgL3RtcC94ZGcteXVtZWthCmV4cG9ydCBESVNQTEFZPTowCmRidXMtcnVuLXNlc3Npb24gc3RhcnR4ZmNlNAoiCgonCg==" | base64 -d > /data/user/0/com.termux/files/home/.termux/widget/dynamic_shortcuts/ArchDesktop
```

> [!NOTE]
> 至此你获得了一个包含 xfce4 桌面的 ArchLinux

接下来可以做什么？

- 将 `/data/user/0/com.termux/files/home/.local/bin/arch` 末尾改为进入普通用户
- 启动桌面
	```
	/data/user/0/com.termux/files/home/.local/bin/arch-desktop
	```
- 把启动脚本放在桌面上
	[[Termux 基础配置]]
- 换一个 shell
	[[fish]]
	[[zsh]]
- 解决缩放问题和其他小问题
	[[其他问题]]