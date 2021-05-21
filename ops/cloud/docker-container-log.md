# docker容器日志

## 查看
可以通过`docker logs`命令查看容器的日志
```bash
[root@iuap-206 containers]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)

```
命令`demo`
```bash
docker logs -f -t --since="2018-02-08" --tail=100 CONTAINER_ID    # 指定时间查看日志，并显示最后100行
docker logs --since 30m CONTAINER_ID    # 查看最近30分钟的日志
docker logs -t --since="2018-02-08T13:23:37" --until "2018-02-09T12:23:37" CONTAINER_ID    # 查看某个时间段的日志
```

## 解决容器日志太多的问题

`docker`容器日志默认的存放路径为`/var/lib/docker/containers/CONTAINER_ID/CONTAINER_ID.json`;
`Docker`在不重建容器的情况下，日志文件默认会一直追加，时间一长会逐渐占满服务器的硬盘的空间，内存消耗也会一直增加;
```bash
# 查找大的日志文件
find . -name *.log | xargs ls -lrth

# 手动清理日志文件
cat /dev/null  > ./2adf2ddd22344dc3962b3082597df83fddfd4b42317469ecdbc83ff653a45d8b/2adf2ddd22344dc3962b3082597df83fddfd4b42317469ecdbc83ff653a45d8b-json.log
```

> 清理日志文件不能直接使用`rm -rf`进行删除，因为在容器运行过程中，对应日志文件一直在被占用，引用计数增加。`rm -rf`只是使日志文件的引用计数-1，文件仍存在，此时`df -h`命令查看磁盘占用没有减少，所以需要使用`cat /dev/null >` 命令来减少文件的磁盘占用

### 控制日志大小

**运行时控制**
```bash
# max-size 最大数值
# max-file 最大日志数
$ docker run -it --log-opt max-size=10m --log-opt max-file=3 redis
```

**全局配置**
修改`/etc/docker/daemon.json`配置参数
```
{
    "log-driver":"json-file",
    "log-opts":{
        "max-size" :"50m","max-file":"1"
    }
}
```
重启服务
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

?> 修改过后，已存在的容器不会生效，必须重新创建运行才可以

!> 修改过后，已存在的容器不会生效，必须重新创建运行才可以

> 修改过后，已存在的容器不会生效，必须重新创建运行才可以