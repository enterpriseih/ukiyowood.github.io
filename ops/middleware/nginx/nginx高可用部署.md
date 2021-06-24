# nginx高可用部署
## 预备
- 官网下载源码包，上传至主机 [http://nginx.org/en/download.html](http://nginx.org/en/download.html)
- 配置yum源

## 源码安装nginx
```bash
#c和c++编译器
#perl的正则库，负责nginx中配置文件的正则解析
#对http包进行gzip压缩，需要nginx.conf中配置gzip on
#openssl库，使用https、md5、sha1散列加密函数时需要
yum install –y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel

#该命令主要是为了设置安装目录和选择启用的模块（检查模块依赖），执行过程中是1. 检查环境，根据环境生成c代码；2.生成编译需要的makefile文件；
#会在当前目录下产生一个objs目录和Makefile：
#前者：存放编译过程中的中间文件和编译后的二进制文件nginx;
#后者：工程使用GNU make来编译连接的描述编译配置文件；
./configure --prefix=/data/nginx_home --with-http_stub_status_module --with-http_ssl_module

# 使用make工具来进行编译和连接，生成nginx二进制可执行文件
make

# 安装程序至nginx_home里，生成对应的配置文件及其他目录文件。
make install 

# 将安装好的nginx目录拷贝一份至10.10.25.101
scp -r /data/nginx_home 10.10.25.101:/data
```

## 安装并配置keepalived
```bash
yum install -y keepalived

# cat /etc/keepalived/keepalived.conf in master 10.10.25.100
! Configuration File for keepalived

global_defs {
#   notification_email {
#     acassen@firewall.loc
#     failover@firewall.loc
#     sysadmin@firewall.loc
#   }
#   notification_email_from Alexandre.Cassen@firewall.loc
#   smtp_server 192.168.200.1
#   smtp_connect_timeout 30
#   router_id LVS_DEVEL
#   vrrp_skip_check_adv_addr
#   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

vrrp_sync_group NGINX_GROUP {
	NGINX
}
vrrp_script chk_nginx {
         script "/etc/keepalived/chknginx.sh"
         interval 1
         weight -20
}

vrrp_instance NGINX {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 150
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
    	10.10.25.250/24 dev eth0 label eth0:1
    }
    track_script {
	chk_nginx
    }
}

# cat /etc/keepalived/keepalived.conf in backup 10.10.25.101
! Configuration File for keepalived

global_defs {
#   notification_email {
#     acassen@firewall.loc
#     failover@firewall.loc
#     sysadmin@firewall.loc
#   }
#   notification_email_from Alexandre.Cassen@firewall.loc
#   smtp_server 192.168.200.1
#   smtp_connect_timeout 30
#   router_id LVS_DEVEL
#   vrrp_skip_check_adv_addr
#   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

vrrp_sync_group NGINX_GROUP {
	NGINX
}

vrrp_script chk_nginx {
         script "/etc/keepalived/chknginx.sh"
         interval 1
         weight -20
}

vrrp_instance NGINX {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
    	10.10.25.250/24 dev eth0 label eth0:1
    }
    track_script {
        chk_nginx
    }

}

```
监控nginx的脚本如下：
```
# cat /etc/keepalived/chknginx.sh
#!/bin/bash

NGX_HOME=/data/nginx_home
run=`ps -C nginx --no-header | wc -l`
if [ $run -eq 0 ];then
     $NGX_HOME/sbin/nginx -s stop
     $NGX_HOME/sbin/nginx
     sleep 3
     if [ `ps -C nginx --no-header | wc -l` ];then
        killall keepalived
     fi
fi
```
