# centos7下编译安装python3.9

> 全部操作都在`root`下执行

## 安装编译相关工具

```bash
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
yum install libffi-devel -y
```

## 下载安装包解压

```bash
mkdir ~/python-src-39 && cd ~/python-src-39
wget https://www.python.org/ftp/python/3.9.2/Python-3.9.2.tar.xz
tar xvfJ Python-3.9.2.tar.xz && cd Python-3.9.2
```

## 编译安装

```bash
mkdir /usr/local/python39
./configure --prefix=/usr/local/python39
make && make install
```

## 创建软连接

```bash
ln -s /usr/local/python39/bin/python3 /usr/local/bin/python3
ln -s /usr/local/python39/bin/pip3 /usr/local/bin/pip3
```