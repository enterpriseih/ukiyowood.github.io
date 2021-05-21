# Dockerfile配置

## 用法
```bash
docker build -t test/demo:tag .
```

## 语法

### FROM
```dockerfile
# 指定基础镜像，如果没有标签，默认为latest
FROM rackspacedot/python37
```

### MAINTAINER
```dockerfile
# 描述镜像的创建者，名称和邮箱
MAINTAINER wushshf wushshf@yonyou.com
```

### RUN
```dockerfile
# 每个RUN命令执行完毕后会成为一个新镜像（层），所以最好多个命令使用一行完成，及时做清理。
# 执行的命令不会调用shell，不会继承环境变量，所以调用命令要用绝对路径
RUN "/bin/bash" "echo" "helloworld"
```

### CMD
```dockerfile
# 一个dockerfile中只能由1个，多个会以最后的为准。
# 为启动容器时提供默认的命令选项
# 如果docker run时提供了命令项会覆盖掉dockerfile中的CMD
CMD ["/bin/bash" "start.py"]
```

### EXPOSE
```dockerfile
# 告诉dockerd容器要对外映射的端口号，便于docker run -p使用
EXPOSE 80
```

### ENV
```dockerfile
# 设置容器的环境变量，可以让其后面的RUN命令使用，容器运行的时候这个变量也会保留。
ENV key1=val1 key2=val2
ENV key val
```

### ADD & COPY
```dockerfile
# 作用基本相同，用于向容器中添加指定的文件活目录
# ADD支持添加网络路径，COPY只能添加本地文件路径
# 如果源是目录，只会复制目录里的内容；
# ADD如果源是一个压缩文件，会自动解压。COPY不是
# 建议还是用ADD来添加文件
ADD src dst
COPY src dst
```

### ENTRYPOINT
```dockerfile
# 这个命令和CMD命令一样，唯一的区别是不能被docker run命令的执行命令覆盖，如果要覆盖需要带上选项--entrypoint，如果有多个选项，只有最后一个会生效。
ENTRYPOINT ["/bin/bash" "entrypoint.sh"]
```

### VOLUME
```dockerfile
# 做容器路径挂载，一般不用，使用docker run -v代替
VOLUME /mnt/path
```

### USER
```dockerfile
# 指定运行容器时的用户名或UID，后续的RUN、CMD、ENTRYPOINT也会使用指定的用户运行命令。
USER demo
```

### WORKDIR
```dockerfile
# 为RUN、CMD、ENTRYPOINT指令配置工作目录。可以使用多个WORKDIR指令，后续参数如果是相对路径，则会基于之前的命令指定的路径。如：WORKDIR  /home　　WORKDIR test 。最终的路径就是/home/test。path路径也可以是环境变量，比如有环境变量HOME=/home，WORKDIR $HOME/test也就是/home/test。
# 指定后也相当于cd
WORKDIR /usr/local/app
```

### ONBUILD
```dockerfile
# 配置当前所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。意思就是，这个镜像创建后，如果其它镜像以这个镜像为基础，会先执行这个镜像的ONBUILD命令。
ONBUILD ["/usr/bin/ls"]
```

## Dockerfile常用配置模板
###