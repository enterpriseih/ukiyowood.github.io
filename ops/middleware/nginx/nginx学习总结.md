# nginx学习总结
## 源码部署
1. 解压源码包
2. 安装依赖系统包

```bash
#c和c++编译器
#perl的正则库，负责nginx中配置文件的正则解析
#对http包进行gzip压缩，需要nginx.conf中配置gzip on
#openssl库，使用https、md5、sha1散列加密函数时需要
yum install –y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
```
3. 预编译
```bash
#该命令主要是为了设置安装目录和选择启用的模块（检查模块依赖），执行过程中是1. 检查环境，根据环境生成c代码；2.生成编译需要的makefile文件；
#会在当前目录下产生一个objs目录和Makefile：
#前者：存放编译过程中的中间文件和编译后的二进制文件nginx;
#后者：工程使用GNU make来编译连接的描述编译配置文件；
./configure --prefix=/root/nginx/nginx_home --with-http_stub_status_module --with-http_ssl_module
```
4. 编译
```bash
# 使用make工具来进行编译和连接，生成nginx二进制可执行文件
make
```
> 如果是为了添加模块进行重新编译，这一步生成了`nginx`文件直接替换到`nginx_home`下就可以了。如果执行下面的`make install`会重置旧的`nginx`配置文件；
5. 安装
```bash
# 安装程序至nginx_home里，生成对应的配置文件及其他目录文件。
make install 
```
6. 配置环境变量，启动
```bash
# 将NGX_HOME/sbin加入PATH变量里
# 执行./sbin/nginx即可启动
```
## 常用配置
### 1. 反向代理配置
```nginx
upstream ncc {
    server 10.10.18.114:1234;
}
server {
    location /nccloud {
        proxy_set_header Host $host;
        #proxy_set_header Host $host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header REMOTE-HOST $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://ncc/nccloud;
    }
}
```

### 2. nginx配置https
#### 生成自签名证书
```bash
mkdir ssl && cd ssl
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout signed.key -out signed.crt
```
#### nginx配置
```nginx
# HTTPS server
server {
   listen       443 ssl;
    server_name  localhost;

    ssl_certificate      cert.pem;
    ssl_certificate_key  cert.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

   location / {
        root   html;
        index  index.html index.htm;
    }

server {
    listen       80;
    server_name  localhost;	
    return 301 https://10.10.18.114$request_uri;
}
```

## nginx集群
[https://www.cnblogs.com/zhangxingeng/p/10721083.html#auto_id_31](https://www.cnblogs.com/zhangxingeng/p/10721083.html#auto_id_31)