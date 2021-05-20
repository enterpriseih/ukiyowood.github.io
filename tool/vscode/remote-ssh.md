# vscode的remote-ssh服务器端vscode-server安装失败

在安装的时候要到`update.code.visualstudio.com`获取资源下载。目标主机连不上

## 错误解决
在`vscode`中输出的内容有1个`commit-id`，这个`id`对应了`github`上`vscode`某个`release`版本的`id`，直接本地访问下载包`https://update.code.visualstudio.com/commit:08a217c4d27a02a5bcde898fd7981bda5b49391b/server-linux-x64/stable`上下载后放到服务器上`~/.vscode-server/bin/<commit-id>`

!> 重要提示