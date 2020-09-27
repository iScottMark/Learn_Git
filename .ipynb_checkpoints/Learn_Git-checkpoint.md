# Learn Git

## 0 Git 简介

![structure](structure.jpg)

### 0.1 git 用户配置
`$ git config --global user.name "Your Name"`

`$ git config --global user.email "email@example.com"`

### 0.2 创建版本库
`$ git init`	初始化一个Git仓库/版本库

`$ git add <file1> <file2>`	提交文件到**暂存区**可以反复多次使用，添加多个文件

`$ git commit -m <message>`	提交修改到**分支**,如master上


## 1 时光穿梭机
`$ git status`	查看工作区的状态

`$ git diff <file>`		查看怎么修改的

`$ cat <file>`	查看文件内容

### 1.1 版本回退
`$ git log` 和 `git log --pretty==oneline`	查看提交，**回到过去**

`$ git reflog`	查看命令历史，**穿梭未来**

`$ git reset --hard HEAD^`	回退到上一个版本

`$ git reset --hard commit_id`回退到指定commit_id的版本

`HEAD` 当前版本

`HEAD^` 上个版本

`HEAD^^` 上上个版本

`HEAD~100` 上100个版本

### 1.2 管理修改
`$ git diff <file>`		比较暂存区和工作区

`$ git diff HEAD -- <file>`	比较版本库的最新版本和工作区

### 1.3 撤销修改
`$ git restore <file>`	丢弃工作区的改动

`$ git restore -staged <file>`	假如已经`$ git add <file>`，可以取消暂存，回退到工作区状态，
若要继续丢弃工作区的改动，则可以再用上述`$ git restore <file>`	命令

### 1.4 删除文件
`$ rm <file>`	删除文件

情形1：的确想从版本库中删除该文件，`git rm test.txt`，同时`$ git commit`提交删除记录

* 此时，若想再恢复则得回退版本；
* 但若要保存当前版本号，且恢复该文件，可用`git checkout commit_id <file>`从旧版本号里
拿出该文件放到当前版本号

情形2：不小心误删了，想恢复该文件，`git checkout -- <file>`


## 2 远程仓库
**Git的杀手级功能之一**

本地的Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以要

**配置SSH**


* 创建SSH Key：`$ ssh-keygen -t rsa -C "your email@example"`

* 登陆GitHub，进行SSH Key的相关设置

* 第一次使用Git的`clone`和`push`会有相关的警告

C:/Users/XiaoM/.ssh 下的`id_rsa`和`id_rsa.pub`是SSH Key的密钥
`id_rsa`是私玥，`id_rsa.pub`是公玥

GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，
只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

### 2.1 添加远程库
现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，
并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。

`$ git remote add origin git@server-name:path/repo-name.git` 关联一个远程库（默认名称为`origin`）

`$ git push -u origin master` 关联后，第一次推送`master`分支的所有内容

> `-u`参数，Git不但会把本地的`master`分支内容推送到远程新的`master`分支，
还会把本地的`master`分支和远程的`master`分支**关联**起来，在以后的推送或者拉取时就可以简化命令。

`$ git push origin master` 此后，每次本地提交后，只要有必要，就可以使用该命令推送最新修改；


### 2.2 从远程库克隆
`$ git clone git@server-name:path/repo-name.git`

e.g.

`$ git clone git@github.com:iScottMark/gitskills.git`

或

`$ git clone https://github.com/iScottMark/gitskills.git`

> Git支持多种协议，包括`https`，但`ssh`协议速度最快。
使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，
但是在某些只开放http端口的公司内部就无法使用`ssh`协议而只能用`https`。

* clone太慢，尝试用`$ git config --global http.postBuffer 524288000`解决


## 3 分支管理

### 3.1 创建与合并分支

![branch](branch.png)

~~`$ git checkout -b <name>`加上`-b`参数表示创建并切换到分支`<name>`，相当于以下两条命令~~

`$ git switch -c <name>` 加上`-c`参数表示创建并切换到新的<name>分支，相当于以下两条命令

`$ git branch <name>` + `$ git switch <name>`

`git branch` 查看分支

`$ git merge <name>` 合并指定的`<name>`分支到当前分支（即`master`分支）

* 这是`Fast-forward`合并方式，还有其他合并方式

`$ git branch -d <name>` 删除`<name>`分支

### 3.2 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

`$ git log --graph` 可以查看分支合并图

`$ git log --graph --pretty=oneline --abbrev-commit`
简约输出

### 3.3 分支管理策略

使用`Fast-forward`合并方式，删除分支后，容易丢失分支的信息，故我们可以采用 `no-ff`合并方式。

`$ git merge --no-ff -m "merge with no-ff" dev`效果如下图

![branch2](branch2.png)

**分支策略**

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![branch2](branch_strategy.png)

### 3.4 Bug分支

情形：你在`dev`分支上进行工作，但是`master`分支上有个紧急bug101需要你去处理，为了不丢失`dev`的工作进度，可以这样做

```
$ git stash
$ git switch master
$ git switch -c issue-101
$ vim 文件B  把bug 改成 fix bug！
$ git add 文件B
$ git commit -m "fix bug 101"
$ git switch master # 切回master分支
$ git merge --no-ff -m "merged bug fix 101" issue-101  # 合并
$ git branch -d issue-101 # 删除
```

现在我们再回到`dev`分支

`$git stash pop` 恢复之前的工作进度，且删除stash中的内容

该条命令也可以细化成

```
$ git stash list  # 查看多个stash的内容
$ git stash apply stash@{id} # 选择指定的stash_id恢复
$ git stash drop  # 删除stash内容
```

* `$ git stash`保存时，一定要将修改过的文件进行`$ git add`，最后在这之前用`$ git status`查看一遍

* 在哪个分支上进行bug的修复，以及创建和合并分支，要切换到对应的分支上

* 此情境下，若想把`master`上的Bug也提交到`dev`上，可以用`$ git cherry-pick <commit_id>`来减少手动重复修改的操作。但要注意的是**针对同一个文件，如`readme.txt`，在`dev`分支下做了修改，然后`stash`，但是bug也出现在`master`分支下的`readme.txt`文件，这样再使用`$ git cherry-pick`就会造成冲突，需要手动`merge`）**

### 3.5 Feature分支
