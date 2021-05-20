# Linux常用命令

[TOC]

## 系统服务
```bash
systemctl list-units --all --type=service    # 列出当前所有的系统服务和状态
```

> 参考文档：[https://mp.weixin.qq.com/s/qDx7alLELPa7vzcaDmfJbw](https://mp.weixin.qq.com/s/qDx7alLELPa7vzcaDmfJbw)

##  系统用户
```bash
echo "password" | passwd testuser --stdin > /dev/null 2>&1    # 直接修改密码-无交互
```

## grep
```bash
grep -Ev "^$|[#;]" conf/bootstrap.conf    # 忽略注释行和空行
```

### 修改时间
```
date -s "2020-07-07 11:35:00"

clock -w
```
### 做文件的MD5生成和校验
> MD5全称报文摘要算法（Message-DigestAlgorithm 5）[RFC 1321]，该算法对任意长度的信息进行逐位计算，产生一个二进制长度128位（十六进制长度32位）的校验和（或称“指纹”，“报文摘要”），不同的文件内容生成相同的报文摘要的概率是极其小的。linux下可以使用md5sum命令工具进行md5的计算和校验，系统安装后自带。
```
md5sum a.txt > checksum/a.md5    #生成文件a.txt的校验文件a.md5
md5sum -c checksum/a.md5    #校验文件的md5码，需要将校验文件和源文件放在同一目录下
```
### shell脚本中调用其他脚本的方式
- fork： 新开一个子Shell执行，子shell可以从父shell中继承环境变量，但是子shell中的环境变量不会带回给父Shell
- exec：在同一个 Shell 内执行，但是父脚本中 `exec` 行之后的内容就不会再执行了。
- source：在同一个 Shell 中执行，在被调用的脚本中声明的变量和环境变量, 都可以在主脚本中进行获取和使用，相当于合并两个脚本在执行。

### 网络连接
```bash
# 查看当前系统tcp连接状态数量统计
netstat -an | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```