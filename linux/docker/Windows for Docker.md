[github.com/dockur/windows](https://github.com/dockur/windows)

建议使用 docker, 而不是 podman

安装 windows7 示例

```bash
docker run -d \         
 --name windows-7 \  
 -e "VERSION=7u" \  
 -e "DISK_SIZE=10G" \  
 -p 8006:8006 \  
 --device=/dev/kvm \  
 --device=/dev/net/tun \  
 --cap-add NET_ADMIN \  
 -v /home/yumeka/Dev/_docker/win7:/storage \  
 -v /home/yumeka/Dev/_docker/win7-share:/shared \  
 --stop-timeout 120 \  
 docker.io/dockurr/windows
```