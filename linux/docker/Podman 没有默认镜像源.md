**拉取镜像时手动指定镜像源**

```bash
podman pull docker.io/mysql:latest
```

**或者编辑配置文件**

```bash
sudo vim /etc/containers/registries.conf
```

添加行

```toml
unqualified-search-registries = ["registry.fedoraproject.org", "registry.redhat.io", "quay.io", "docker.io"]
```

或者

```toml
unqualified-search-registries = ["docker.io"]
```