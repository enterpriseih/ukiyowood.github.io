# docker troubleshooting

## docker容器中时间与宿主机时间不一致的问题

### 现象
发现容器中`date`命令的结果与宿主机`date`命令结果不一致。
```
root@63e0fc920055:/opt/app# date
Thu Jan 28 06:40:29 UTC 2021
```
```
[root@yum-17 build-ncc-platform]# date
Thu Jan 28 14:43:55 CST 2021
```
### 分析
容器与宿主机上的时间相差约8h，时区不一样，可以通过修改时区进行配置。

### 方案
1. 启动容器时将宿主机的`/etc/localtime`挂载到容器中
```bash
docker run -d --name=demo -v /etc/localtime:/etc/localtime:ro -v /etc/timezone:/etc/timezone demo/image:tag
```
2. 在做镜像的时候将时区调整
```dockerfile
#设置时区
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
&& echo 'Asia/Shanghai' >/etc/timezone \
```

