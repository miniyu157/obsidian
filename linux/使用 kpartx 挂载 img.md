> 安装 `multipath-tools` 包
> 
> ```bash
> sudo pacman -S multipath-tools
> ```

**挂载**

创建映射

```bash
sudo kpartx -a data.img
```

查看块设备信息

```bash
lsblk /dev/loop0
```

将某个分区挂载到指定文件夹

```bash
sudo mount /dev/mapper/loop0p1 /mnt/img_data
```

**卸载**

卸载分区

```bash
sudo umount /mnt/img_data
```

删除设备映射

```bash
sudo kpartx -d data.img
```



