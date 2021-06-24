# win10下UWP应用设置代理

1. `regedit`命令打开注册表管理器
2. 定位到路径`HKEY_CURRENT_USER\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppContainer\Mappings `接着在左边的注册表项中找到你想解除网络隔离的应用（这里以UWP版的推特为例），右边的 `DisplayName` 就是应用名称，而左边那一大串字符就是应用的 SID 值了。
3. 管理员权限打开cmd，执行命令`CheckNetIsolation.exe loopbackexempt -a -p=SID`，（SID就是上一步复制的SID），出现「完成」后就大功告成了。
![](_v_images/20201208170350528_10538.png)