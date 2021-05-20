# git常用命令和操作

## 配置

```Bash
$ git config --global user.name "runoob"  # 配置用户
$ git config --global user.email test@runoob.com #

$ git config --global credential.helper store  # 保存git配置，解决每次push都要输入用户名和密码的问题

git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'

$ git config --list           # 查看配置

```

## git 分支操作

```Bash
git branch                    # 查看分支
git branch feature-1          # 创建本地分支
git checkout feature-1        # 切换到分支
git checkout -b dev           # 创建并切换到一个新的分支
git push origin dev           # 提交到远程仓库分支
git branch -d mod1            # 删除本地分支mod1
```

## git merge合并操作

```Bash
git merge [branch_name]       # 把指定的分支合并到当前分支
```

## 版本回退

### git reset回退

```Bash
# reset是回退到指定的某个版本，指定版本后面的提交都没了

$ git reset --hard HEAD^        # 回退到上个版本
$ git reset --hard commit_id    # 退到/进到 指定commit_id

$ git push --force   # 强制推送到远程，千万不要拉取，否则又会把最新提交拉下来
```

### git revert 重做版本

```bash
# revert是将指定的提交回滚了，变成新的提交，不影响其他的提交

git revert -n commit_id
git commit -m 'notes'
```