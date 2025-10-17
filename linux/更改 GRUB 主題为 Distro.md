https://arch.icekylin.online/guide/advanced/beauty-2.html#_7-1-%E8%AE%BE%E7%BD%AE-grub-%E4%B8%BB%E9%A2%98

**简明扼要**
前往 [github.com/AdisonCavani/distro-grub-themes](https://github.com/AdisonCavani/distro-grub-themes) 下载对应发行版压缩包，解压后 cd 至解压后的文件夹内。

然后执行 `sudo cp -rf . /usr/share/grub/themes/Distro` 复制到 GRUB 默认文件夹内

编辑 `/etc/default/grub` 将行 `#GRUB_THEME=` 改为 `GRUB_THEME="/usr/share/grub/themes/Distro/theme.txt"`

执行 `grub-mkconfig -o /boot/grub/grub.cfg` 以更新配置文件

