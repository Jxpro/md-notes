## 格式化输出容器

```bash
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Image}}\t{{.Command}}\t{{.Names}}\t{{.Status}}"

docker ps -a --format "table {{.Command}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
```

## 删除所有容器和镜像

```bash
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
```

