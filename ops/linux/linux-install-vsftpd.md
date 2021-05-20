# centos7下部署ftp服务并启用虚拟用户配置

[TOC]

## 准备工作
配置好`yum`仓库，可以安装`vsftpd`及其依赖包
关闭防火墙和`selinux`或者配置好对应的放行策略
> `ftp`服务默认走20/tcp、21/tcp端口。开启被动传输模式还会开启随机端口进行连接

## 开始部署

### 安装`vsftpd`
```bash
yum install -y vsftpd
yum install -y pam* libdb*-skip-broken
```

### 配置

#### 创建虚拟用户临时文件
```bash
# 添加内容，奇数行为用户名，偶数行为密码
# vim /etc/vsftpd/ftpusers.txt
nccftp
1
```

#### 生成虚拟用户数据认证文件
```bash
db_load -T -t hash -f /etc/vsftpd/ftpusers.txt /etc/vsftpd/login.db
chmod 755 /etc/vsftpd/login.db
```

#### 配置`pam`认证文件
```bash
# vim /etc/pam.d/vsftpd
## 注释掉原来的内容，添加以下内容
auth required pam_userdb.so db=/etc/vsftpd/login
account required pam_userdb.so db=/etc/vsftpd/login
```

#### 创建虚拟用户的映射
```bash
# 这个用户是本地系统用户，不需要登录。客户端使用虚拟用户登录后相当于使用这个ftpuser用户进行登录访问操作系统
useradd -s /sbin/nologin ftpuser
```

#### 创建虚拟用户配置文件放置的目录
```bash
mkdir -p /etc/vsftpd/user_conf
```

#### 配置`vsftpd`主配置文件
```bash
# vim /etc/vsftpd/vsftpd.conf
## 修改配置文件中以下配置项
anonymous_enable=NO
xferlog_file-/var/log/xferlog
listen=YES
listen_ipv6=NO

## 以下配置项添加到文件尾部
guest_enable=YES
guest_username=ftpuser
user_config_dir=/etc/vsftpd/user_conf
virtual_use_local_privs=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES
```

#### 创建虚拟用户的配置文件
```bash
# vim /etc/vsftpd/user_conf/nccftp
local_root=/data/ftp_data    # 这个目录指定的是你用该虚拟用户信息登录后进到的系统目录，后面记得要给ftpuser:ftpuser属主授权
write_enable=YES
anon_world_readable_only=NO
anon_upload_enable=NO
anon_mkdir_write_enable=NO
anon_other_write_enable=NO
```

#### 配置虚拟用户的主目录
```bash
mkdir -p /data/ftp_data
chown -R ftpuser:ftpuser /data/ftp_data
```

#### 启动服务
```bash
systemctl enable --now vsftpd
systemctl status vsftpd
```