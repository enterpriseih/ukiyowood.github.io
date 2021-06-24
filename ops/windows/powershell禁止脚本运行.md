# powershell禁止脚本运行

Powershell对脚本的执行策略有以下4种：
- `Restricted` 禁止运行任何脚本和配置文件。默认！
- `AllSigned` 可以运行脚本，但要求所有脚本和配置文件由可信发布者签名，包括在本地计算机上编写的脚本。
- `RemoteSigned` 可以运行脚本，但要求从网络上下载的脚本和配置文件由可信发布者签名；不要求对已经运行和已在本地计算机编写的脚本进行数字签名。
- `Unrestricted` 可以运行未签名脚本。（危险！）  

## 修改操作
```powershell
# 管理员权限运行

Get-ExecutionPolicy    # 获取当前策略

Set-ExecutionPolicy Unrestricted    # 修改策略
```