# pip国内源配置

## 临时使用国内源pypi镜像
```
pip install -i http://pypi.douban.com/simple/ numpy
pip install -i http://pypi.douban.com/simple/--trusted-host pypi.douban.com  #此参数“--trusted-host”表示信任，如果上一个提示不受信任，就使用这个
```

## 永久配置生效

### Linux平台
1. 创建配置文件`pip.conf`
```
mkdir ~/.pip && cd ~/.pip && vim pip.conf
```
2. 编辑
```
[global] 
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn  # trusted-host 此参数是为了避免麻烦，否则使用的时候可能会提示不受信任
```

### Windows平台
1. 创建配置文件`C:\Users\wushshf\pip\pip.ini`
2. 编辑
```
[global] 
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn  # trusted-host 此参数是为了避免麻烦，否则使用的时候可能会提示不受信任
```