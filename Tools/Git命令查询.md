# Git命令查询

## 系统配置

配置级别有： [ --system | --global | --local ]

```shell
# 生成ssh key
ssh-keygen -t rsa -C "xionglinlin@uniontech.com"
# 显示git配置
git config --list
# 配置提交代码时的用户信息
git config --global user.name "xionglinlin"
git config --global user.email "xionglinlin@uniontech.com"
# 配置默认工具
git config --global core.editor vim/emacs
git config --global diff.tool vimdiff
```

## 初始化和提交

```shell
git init
git add .
git commit -m "first commit"
# 使用上一次commit，如果代码没有任何变化，则用来修改上一次commit信息
git commit --amend
# 修改最近提交用户名和邮箱
git commit --amend --author "user_name <user_email>"
```

用于说明 commit 的类别，一般使用下面7个标识：
- feat：新功能（feature）
- fix：修补bug
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改bug的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

## 推送和拉取

```shell
# 下载远程仓库的所有变动，但不进行合并操作
git fetch origin
# 取回远程仓库的变化，并与本地分支合并
git pull
git pull origin <branch>
# 第一次将本地仓库master分支推送至远程仓库
git push -u origin master
# 以后每次将本地仓库master分支推送至远程仓库
git push origin master
# 推送所有分支到远程仓库
git push origin --all
```

## 远程仓库管理

```shell
# 查看所有远程库信息
git remote -v
# 将本地的版本库与远程库url进行关联，并重命名为origin
git remote add origin <url>
# 解除远程库的关联
git remote rm origin
# 本地与远程关联
git branch --set-upstream-to=origin/<branch> <branch>
```

## 撤销和回退

还未git add

```shell
# 撤销暂存区的修改，将暂存区中文件恢复到工作区，下面两个命令是一样的效果
git checkout -- <file>
git restore <file>
```

已经git add 未git commit

```shell
# 撤销暂存区的修改，用版本库里的文件恢复到暂存区，此时，工作区的内容不变
git reset HEAD <file>
git restore --staged <file>
```

将当前分支的版本库的HEAD重置为指定的commit

```shell
git reset [--soft | mixed | --hard] <commit_id>
# 参数说明：
# --soft 所有commit_id之后的内容都撤销到暂存区，工作区无变化
# --mixed 暂存区重置到commit_id，所有commit_id之后的内容都撤销到工作区
# --hard 暂存区和工作区都重置到commit_id，所有工作区和暂存区的修改都丢弃
# 参考链接：https://segmentfault.com/a/1190000012071372

# 回退到上1版本
git reset --hard HEAD^ 
# 回退到远程master
git reset --hard origin/master
```

用一个新提交来撤销一个历史提交所做的修改

```shell
git revert <commit_id>
```

## 分支操作

```shell
# 列出本地所有分支
git branch
# 列出远程所有分支
git branch -r
# 列出所有本地和远程分支
git branch -a
# 新建一个分支，但依然停留在当前分支
git branch <new branch>
# 根据指定的提交版本号创建分支
git branch <new branch> <commit_id>
# 新建一个分支，与指定的远程分支建立追踪关系
git branch --track <new branch> <remote branch>
# 删除分支
git branch -d <branch>
# 删除远程分支，同名的本地分支并不会被删除，所以还需要单独删除本地同名分支
git push origin --delete <branch>
# 清理本地无效分支(远程已删除本地没删除的分支)
git fetch -p
# 切换到指定分支，并更新工作区
git checkout <branch>
# 新建一个分支，并切换到该分支
git checkout -b <new branch>
# 本地新建分支并关联远程仓库分支
git checkout -b <new branch> origin/dev
# 切换到上一个分支
git checkout -
```

分支合并

```shell
# 将branch的代码合并到当前所在的分支，如果有冲突，手动解决冲突后，在add/commit
git merge <branch>
```

## 保存工作区

```shell
# 将当前工作区的文件修改缓存到一个临时的栈空间，如果新增的文件没有git add 是不会保存的
git stash [save "descriptions"]
# 查看当前存储了哪些修改
git stash list
# 显示做了哪些改动，默认show第一个改动，如果要显示其他的可以加 stash@{num}
git stash show 显示第一个
git stash show stash@{1} 显示第二个
# 从栈空间将数据恢复的工作区，同时将栈空间的stash删除，默认pop第一个改动，如果要pop其他的可以加 stash@{num}
git stash pop 默认pop第一个
git stash pop stash@{1} pop第二个
# 从栈空间将数据恢复的工作区，但不会把栈空间的数据删除，默认apply第一个改动，如果要apply其他的可以加 stash@{num}
git stash apply 默认apply第一个
git stash apply stash@{1} apply第二个
# 丢弃stash@{num}存储，从列表中删除
git stash drop stash@{num}
# 删除所有缓存的stash
git stash clear
```

## 代码对比

```shell
# 显示指定文件是什么人在什么时间修改过
git blame <file>
# 显示暂存区和工作区的代码差异
git diff
# 显示暂存区和上一次commit的差异
git diff --cached <file>
# 显示工作区与当前分支最新版本之间的差异
git diff HEAD -- <file>
# 显示两次提交之间的差异
git diff <commit_id1> <commit_id2> [--name-only]
# 显示两个分支所有有差异的文件的详细差异
git diff <branch1> <branch2>
# 显示两个分支所有有差异的文件列表
git diff <branch1> <branch2> --stat
# 检查是否有空白错误(regex:' \{1,\}$')
git diff --check <file>
# 显示今天你写了多少行代码
git diff --shortstat "@{0 day ago}"
```

## 查看日志

```shell
# 显示当前分支的提交历史
git log [--pretty=oneline]
# 显示所有操作的历史记录，包括回退的记录
git reflog
# 分支branch最近n条的提交历史
git log [branch_name] -n
# 显示commit历史，及每次commit发生的文件变化
git log --stat
#显示新增、修改、删除的文件清单
git log --name-status
# 显示指定文件历次commit的内容增删
git log -p [file]
# 对提交历史单行排列
git log --oneline
# 对提交历史图形化排列
git log --graph
# 显示所有提交过的用户，并按提交次数排序
git shortlog -sn
# 仅显示添加或移除了某个关键字的提交(某些场景比单独git log -p | grep [keyword] 好用很多)
git log -S [keyword]
# 查看某作者在某发布版本最近一个月的提交, 常见于线上背锅
git log origin/b3.3/master --author=yx-ren --since="2019-10-01" --before="2019-11-01"
# 查看某刚离职同事过去一个月的提交, 常见于背锅
git log origin/b3.0/master --author=some_leave --since="1 month ago"
# 过去一周的提交(写周报的时候可以看看我这一周干了啥)
git log --since=1.weeks
# 过去一天的提交(下班的时候可以看看我这一天干了啥)
git log --since=1.days
# 过去1周2天3小时40分50秒之内的提交
git log --since="1 weeks 2 days 3 hours 40 minutes 50 seconds ago"
```

## 清理工作区

```shell
# 查看清理文件列表(不包括文件夹), 不执行实际清理动作
git clean -n
# 查看清理文件列表(包括文件夹), 不执行实际清理动作
git clean -n -d
# 清理所有未track文件和文件夹, 常用
git clean -df
```

## 标签管理

```shell
# 查看标签
git tag 
# 查看指定标签
git show <tag name> 
# 在log中显示标签
git log --pretty=oneline --abbrev-commit --decorate=full 
# 为上次commit位置打标签
git tag <tag name> 
# 为指定commit位置打标签
git tag <tag name> <commit id> 
# 为指定commit打标并添加描述
git tag -a <tag name> -m "descriptions" <commit id> 
# 删除本地标签
git tag -d <tag name> 
# 推送指定标签到远程
git push origin <tag name> 
# 推送所有本地标签到远程
git push origin --tags 
# 删除远程标签（先删除本地标签）
git push origin :refs/tags/<tag name> 
```

## 参考链接

[Git 知识大全](https://gitee.com/help/categories/43)  
[Git Community Book 中文版](http://gitbook.liuhui998.com/index.html)  
[Git教程-廖雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)  
[GIT 常用命令](https://www.cnblogs.com/chenwolong/p/GIT.html)
