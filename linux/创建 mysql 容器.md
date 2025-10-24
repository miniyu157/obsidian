以 Podman 为例

**创建**

```bash
podman create \
  --name my-mysql \
  -e MYSQL_ROOT_PASSWORD=1234 \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  docker.io/mysql:latest
```

其中 `/var/lib/mysql` 是挂载的数据位置

**启动容器**

```bash
podman start my-mysql
```

**进入容器的 mysql 命令行**

```bash
podman exec -it my-mysql mysql -u root -p
```

**进入容器系统**

```bash
podman exec -it my-mysql /bin/bash
```
