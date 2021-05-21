# vscode下配置golang环境

## golang环境配置
```bash
wget https://studygolang.com/dl/golang/go1.16.2.linux-amd64.tar.gz    # 下载对应二进制包
tar xvfz go1.16.2.linux-amd64.tar.gz && mv go /usr/local/go
```
编辑环境变量
```
# vim ~/.bash_profile
## 文件尾部添加
# golang env
export PATH=/usr/local/go/bin:$PATH
```
配置`go`环境变量
```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## 安装`vscode`插件

1. 插件商店搜索`go`，安装插件
2. 打开一个`.go`后缀的文件，安装弹出的建议插件