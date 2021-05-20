# github-webhook工具实现github自动构建

## 原理
当本地`git push origin master`向Github远程仓库提交代码时，可以通过配置github自带webhook向服务器发送请求，利用github-webhook工具在服务器端接到请求后，调用自定义shell脚本来实现自动构建

## 使用

1.下载工具包
```bash
wget http://img.sgfoot.com/github-webhook1.4.1.linux-amd64.tar.gz
```

2. 安装命令
```bash
tar -zxvf github-webhook1.4.1.linux-amd64.tar.gz
cp github-webhook /usr/local/bin/
chmod u+x /usr/bin/github-webhook
```

3. 运行`github-webhook`
```bash
# 非后台运行
github-webhook -b [shell脚本路径] -s [github webhook设置的密码]
# 后台运行
nohup github-webhook -b [shell脚本路径] -s [github webhook设置的密码] &
```

> - 默认端口: 2020
> - 有效访问地址: http://ip:2020/web-hook
> - `-b` 是shell脚本路径参数
> - `-s` 是github webhook设置的密码

4. 到`github`里配置webhooks