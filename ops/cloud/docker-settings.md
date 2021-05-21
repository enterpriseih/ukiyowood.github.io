# dockerd服务配置

## 修改镜像存储路径
```bash
docker info    # 可以查看dockerd目前的存储路径
```

1. 编辑配置文件`/etc/docker/daemon.josn`
```daemon.josn
{
    "graph":"/mnt/docker-data"
}
```
2. 重启服务
```bash
systemctl daemon-reload
systemctl restart docker
```