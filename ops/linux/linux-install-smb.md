# Linux下部署Samba服务

## 1. 安装`samba`
```
yum install -y samba samba-client
```

## 2. 添加用户为smb用户
```
smbpasswd -a root
```

## 3. 修改配置文件
```
[data]
comment = Data share Directories
path = /data/share
browseable = yes
writable = yes
write list = @root
```

```
[data]
comment = Data share Directories
path = /data/share
browseable = yes
read only = yes
#writable = yes
#write list = @root
```

## 4. 重启服务
```
systemctl restart smb
```