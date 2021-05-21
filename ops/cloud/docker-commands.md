# Docker常用命令
## 镜像
```bash
# 删除所有的镜像
docker images | tail -n +2 | awk '{print $3}' | xargs docker rmi -f
# 查询镜像详细信息
docker inspect IMAGE_NAME:TAG
```

## 容器
```bash
docker export -o am-fa.tar CONTAINER_ID    # 将容器导出为文件包，解压出来后可以获取到容器中的文件系统内容
```