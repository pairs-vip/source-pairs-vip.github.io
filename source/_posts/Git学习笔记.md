---
title: Git 学习笔记
date: 2026-11-25 18:00:00
tags:
  - Git
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# Git分布式版本控制工具

### 1.概述

#### 1.1开发中的实际场景

+ 备份
+ 代码还原
+ 协同开发
+ 追溯问题代码的编写人和编写时间

#### 1.2版本控制器的方式

+ 集中式版本控制工具

  集中式版本控制工具，版本库是集中存放在中央服务器的，team里每个人work时从中央服务器下载代码，是必须联网才能工作，局域网或互联网，个人修改后然后提交到中央版本库。

  例如：SVN和CVS

  ![image-20220309112758351](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220309112758351.png)

+ 分布式版本控制工具

  分布式版本控制系统没有"中央服务器"，每个人的电脑上都是一个完整的版本库，这样工作的时候，不需要联网，因为版本库就在自己的电脑上，多人协作只需要各自的修改推送给对方，就能互相看到对方的修改。

  例如：Git

  ![image-20220309112820582](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220309112820582.png)
  
  优点：
  
  ​			速度，简单的设计，对非线性开发模式的强力支持(允许成千上万个并行开发的分支)，完全分布式，有能力高效的管理类似Linux内核一样的超大规模项目(速度和数据量)

#### 1.3Git工作流程

![image-20220309113618787](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220309113618787.png)

命令如下：

clone(克隆)：从远程仓库中克隆代码到本地仓库

checkout(检出)：从本地仓库中检出一个仓库分支然后进行修订

add(添加)：在提交前先将代码提交到暂存区

commit(提交)：提交到本地仓库，本地仓库中保存修改的各个历史版本

fetch(抓取)：从远程库，抓取到本地仓库，不进行任何的合并动作，一般操作比较少

pull(拉取)：从远程仓库到本地仓库，自动进行合并(merge)，然后放到工作区，相当于fetch+merge

push(推送)：修改完成后，需要和团队成员共享代码时，将代码推送到远程仓库



### 2.安装

Git Bash中操作需要使用linux命令：

+ ls/ll	 查看当前目录
+ cat      查看文件内容
+ touch 创建文件
+ vi         vi编辑器

#### 2.1Git环境配置

##### 2.1.1下载与安装

下载地址：https://git-scm.com/download

傻瓜式安装

安装完成后在电脑桌面或者其他目录点击右键，如果能够看到Git GUI Here和Git Bash Here菜单，则说明安装成功。

备注：

​	Git GUI：Git提供的图形界面工具

​	Git Bash：Git提供的命令行工具

当安装Git首先要做的事情是设置用户名名称和email地址，这是非常重要的，因为每次Git提交都会使用该用户信息。

##### 2.1.2基本配置

1.打开Git Bash

2.设置用户信息

```
git config --global user.name"itcash"
git config --global user.email"hello@itcast.cn"
```

​	查看配置信息

```
git config --global user.name
git config --global user.email
```

##### 2.1.3为常用指令配置别名(可选)

有些常用的指令参数非常多，每次都要输入好多参数，我们可以使用别名。

1.打开用户目录，创建**.bashrc**文件

​	部分windows系统不允许用户创建点号开头的文件，可以打开Git Bash,
​	执行**touch ~/.bashrc**

2.在.bashrc文件中输入如下内容

```shell
#用于输出git提交日志
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
#用于输出当前目录所有文件及基本信息
alias ll='ls-al'
```

3.打开gitBash，执行**source ~/.bashrc**

##### 2.1.4解决GitBash乱码问题

1.打开GitBash执行下面指令

```shell
git config --global core.quotepath false
```

2.**${git_home}/etc/bash.bashrc**(Git安装目录下，etc文件夹下的bash.bashrc文件)文件最后加入下面两行()

```shell
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

#### 2.2获取本地仓库

要使用Git对我们的代码进行版本控制，首先需要获得本地仓库

1）在电脑任意位置创建一个空目录(例如test)作为我们的本地Git仓库

2）进入到这个目录中，右键打开Git Bash窗口

3）执行命令git init    （初始化当前目录为一个git仓库）

4）成功之后可以在文件夹下看到隐藏的.git目录

#### 2.3基础操作指令

Git工作目录下对文件的修改(增加，删除，更新)会存在几个状态，这些修改的状态会随着我们执行Git命令而发生变化。

![image-20220309213759282](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220309213759282.png)

利用命令来控制这些状态之间的转换：

1.git add	(工作区 ---> 暂存区)

2.git commit	(暂存区  ---->   本地仓库)

##### 2.3.1查看修改的状态(status)

+ 作用：查看修改的状态(暂存区，工作区)
+ 命令形式：git status

##### 2.3.2添加工作区到暂存区(add)

+ 作用：添加工作区一个或多个文件的修改到暂存区
+ 命令形式：git add 单个文件名|通配符
  + 将所有修改加入暂存区：git add .

##### 2.3.3提交暂存区到本地仓库(commit)

+ 作用：提交暂存区内容到本地仓库的当前分支
+ 命令形式：git commit -m '注释内容'

##### 2.3.4查看提交日志(log)

在之前的配置别名git-log就包含了这些参数，所以后续可以直接使用指令git-log

+ 作用：查看提交纪录
+ 命令形式：git log [option]
  + options
    + --all	显示所有分支
    + --pretty=oneline   将提交信息显示为一行
    + --abbrev-commit   使得输出的commitId更简洁
    + --graph   以图的形式显示

##### 2.3.5版本回退

+ 作用：版本回退
+ 命令形式：git reset --hard commitID
  + commitID 可以用git-log或git log指令查看
+ 查看已经删除的纪录
  + git reflog
  + 这个指令可以看到已经删除的提交纪录

##### 2.3.6添加文件到忽略列表

一般我们总会有些文件无需纳入Git的管理，也不希望他们总出现在未跟踪文件列表，通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等，在这种情况下，我们可以在工作目录中创建一个名为**.gitignore**的文件(文件名称固定)，列出要忽略的文件模式。例如：

![image-20220309222256240](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220309222256240.png)

#### 2.4分支

几乎所有的版本控制系统都以某种形式支持分支。使用分支意味着你可以把你的工作从开发主线上分离开来进行重大的Bug修改，开发新的功能，以免影响开发主线。

##### 2.4.1查看本地分支

+ 命令：git branch

==Head指向的分支为当前分支。==

##### 2.4.2创建本地分支

+ 命令：git branch 分支名

##### 2.4.3切换分支

+ 命令：git checkout 分支名

==也可以直接切换到一个不存在的分支，此时将会自动创建此分支并切换过去。==

+ ==命令：git checkout -b 分支名==

##### 2.4.4合并分支

一个分支上的提交可以合并到另一个分支

+ 命令：git merge 分支名称

+ 例如：![image-20220310132456561](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310132456561.png)

意思是将dev01分支上的提交合并到master上来

一般都是将其他分支合并到master上。

**合并的快进模式：**

![image-20220310135641597](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310135641597.png)

##### 2.4.5删除分支

不能删除当前分支，只能删除其他分支

+ 命令：git branch -d 分支名                     删除分支时，需要做各种检查
+ 命令：git branch -D 分支名                     不做任何检查，强制删除

##### 2.4.6解决冲突

当两个分支上对文件的修改可能会存在冲突，例如同时修改了同一个文件的同一行，这时就需要手动解决冲突，步骤如下：

1.处理文件中冲突的地方

2.将解决完冲突的文件加入暂存区(add)

3.提交到仓库(commit)

![image-20220310133609013](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310133609013.png)

##### 2.4.7开发中分支使用原则与流程

在开发中，一般有如下几个分支使用原则与流程：

+ master(生产)分支

  线上分支，主分支，中小规模项目作为线上运行的应用对应的分支

+ develop(开发)分支

  是从master创建的分支，一般作用开发部门的主要开发分支，如果没有其他并行开发不通期上线要求，都可以在此版本进行开发，阶段开发完成后，需要合并到master分支，准备上线。

+ feature/xxxx分支

  从develop创建的分支，一般时同期并行开发，但不同期上线时创建的分支，分支上的研发任务完成后合并到develop分支。

+ hotfix/xxxx分支

  从master排生的分支，一般作为线上bug修复使用，修复完成后需要合并到master,test,develop分支。

+ 还有一些其他分支，例如test分支(用于代码测试),pre分支(预上线分支)等等。

![image-20220310134557192](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310134557192.png)

### 3.Git远程仓库

#### 3.1常见的托管服务(远程仓库)

GitHub，码云，GitLab等

GitHub(https://github.com/)是一个面向开源及西游软件项目的托管平台，因为只支持Git作为唯一的版本库格式进行托管，故名GitHub

码云(https://gitee.com/)是国内的一个代码托管平台，由于服务器在国内，所以相比于GitHub,码云速度更快

GitLab(https://about.gitlab.com/)是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务，一般用于在企业，学校内部网络搭建git私服。

**此处以码云为例，GitHub和码云基本一样。**

#### 3.2注册码云

#### 3.3创建远程仓库

![image-20220310142533634](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310142533634.png)

#### 3.4配置SSH公钥

+ 生成SSH公钥
  + 在git bash中输入指令  ssh-keygen -t rsa
  + 一直敲回车
    + 如果公钥已存在，则自动覆盖
+ Gitee设置账户公钥
  + 获取公钥
    + git bash中输入指令 cat ~/.ssh/id_rsa.pub
  + ![image-20220310142857768](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310142857768.png)
  + 验证是否配置成功
    + git bash 中输入指令 ssh -T git@gitee.com

#### 3.5操作远程仓库

##### 3.5.1添加远程仓库

此操作时先初始化本地库，然后与已创建的远程库进行对接。

+ 命令：git remote add <远端名称> <仓库路径>
  + 远端名称，默认是origin，取决于远端服务器的设置
  + 仓库路径，从远端服务器获取此url
  + 例如：git remote add origin giit@gitee.com:czbk_zhang_meng/git_test.git

##### 3.5.2查看远程仓库

+ 命令：git remote

##### 3.5.3推送到远程仓库

+ 命令：git push [-f] [--set-upstream] [远端名称 [本地分支名]\[:远端分支名]]
  + 如果远程分支名和本地分支名相同，则可以只写本地分支
    + git push origin master
  + **-f**  表示强制覆盖
  + **--set -upstream**推送到远端的同时并且建立起和远端分支的关联关系。
    + git push --set-upstream origin master
  + 如果**当前分支已经和远端分支关联**，则可以省略分支名和远端名
    + git push 将master分支瑞推送到已关联的远端分支

##### 3.5.4本地分支与远程分支的关联关系

+ 查看关联关系
  + 命令：git branch -vv
  + 可以看到远程和本地的分支及关系

![image-20220310150734189](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310150734189.png)

##### 3.5.5从远程仓库克隆

如果已经有一个远端仓库，我们可以直接clone到本地

+ 命令：git clone <仓库路径> [本地目录]
  + 本地目录可以省略，会自动生成一个目录

##### 3.5.6从远程仓库中抓取和拉取

远程分支和本地分支一样，我们可以进行merge操作，只是需要把远端仓库的更新都下载到本地，在进行操作。

+ 抓取 命令：git fetch [remote name] [branch name]
  + **抓取指令就是将仓库中的更新都抓取到本地，不会进行合并**
  + 如果不指定远端名称和分支名，则抓取所有分支
+ 拉取 命令：git pull [remote name] [branch name]
  + **拉取指令就是将远端仓库的修改拉到本地并自动进行合并，等同于fetch+merge**
  + 如果不指定远端名称与分支名，则抓取所有并更新当前分支

##### 3.5.7解决合并冲突

在一段时间内，A，B用户修改了同一个文件，且修改了同一行位置的代码，此时会发生合并冲突。

A用户在本地修改代码后有限推送到远端仓库，此时B用户在本地修改代码，提交到本地仓库后，也需要推送到远程仓库，此时B用户晚于A用户，**故需要先拉取远程仓库的提交，经过合并后才能推送到远端分支**，如下图所示：

![image-20220310152441200](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220310152441200.png)

在B用户拉取代码时，因为A，B用户同一时间修改了同一个文件的相同位置代码，所以发生合并冲突。

**远程分支也是分支，所以合并时冲突的解决办法和本地分支合并冲突的解决办法相同。**

### 4.在Idea中使用Git

#### 4.1在Idea中配置Git

安装好Idea后，如果Git安装在默认路径下，那么idea会自动找到git的位置，如果更改了Git的安装位置则需要手动配置Git的路径，选择File->Settings打开设置窗口，找的Version Control下的git选项：

![image-20220311124206864](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311124206864.png)

点击Test按钮，执行成功则配置完成。

#### 4.2在Idea中操作Git

场景：本地已经有一个项目，但并不是Git项目，我们需要将这个项目放到远程仓库，和其他开发人员继续一起协作开发。

##### 4.2.1创建一个远程仓库

##### 4.2.2初始化本地仓库

![image-20220311125211869](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311125211869.png)

##### 4.2.3设置远程仓库

![image-20220311125835876](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311125835876.png)

![image-20220311125944414](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311125944414.png)

##### 4.2.4提交到本地仓库

![image-20220311130148726](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311130148726.png)

##### 4.2.5推送到远程仓库

![image-20220311130517761](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311130517761.png)

##### 4.2.6克隆到本地仓库

![image-20220311131349489](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311131349489.png)

去远程仓库那里复制clone的ssh或者https

![image-20220311131417169](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311131417169.png)

##### 4.2.7协同工作

两个人对同一代码进行修改，可能会出现合并冲突，具体处理方式和之前处理方法类似。

在Idea中新建分支：在Log中之间选择提交的某一个版本右键就可以在此处创建分支。

在Idea中切换或者合并分支：在右下角有一个Git：分支名，点他可进行分支相关的操作。

#### 4.3IDEA常用的Git操纵入口

![image-20220311133241138](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311133241138.png)

![image-20220311133303106](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311133303106.png)

**铁令：**

1.切换分支前先提交本地的修改

2.代码及时提交，提交过了就不会丢

#### 4.4IDEA集成Git Bash作为Terminal

![image-20220311142924420](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/git/image-20220311142924420.png)





## 利用Git向GitHub上传文件详细步骤(不同于上面教程)

1.进入 GitHub官网，登录个人账号，新建一个Git仓库



2.创建完成之后，点击Clone or download，复制下面的链接（ssh或https）



3.在电脑上任意盘创建一个文件夹，用来 保存管理 想要上传GitHub的文件。（最好取一个与GitHub上新建的那个仓库一样或相近的名字，方便以后 查找管理）



4.确保电脑安装了Git软件



5.鼠标右键，点击Git Bash Here , 进入Git



6.利用cd命令跳转到刚才新建的文件夹



7.接下来使用git clone命令，目的是将GitHub上面项目 的 Git 仓库复制一份到本地刚创建的文件夹，后面的链接是步骤2复制的链接（粘贴时注意，Ctrl+v不行，只能鼠标右键 --> paste）

**例如：git clone 链接**



8.接着进入刚刚创建的文件夹，文件夹里会出现一个名为远程仓库名的文件夹（这个文件夹及其内容就是通过步骤7命令，从GitHub上复制下来的），点击进去可以查看里面的文件有哪些

在Git中，利用cd命令，进入这个文件夹，然后使用命令ls -al查看里面所有的文件

除了有一个README.md文件，还有个.git文件，可能在电脑上看不到，因为这个.git文件是一个隐藏文件，查看 --> 隐藏的项目



9.此之前的步骤，是针对第一次创建GitHub仓库的。其目的就是复制GitHub上的仓库到本地PC端，使之与GitHub相连，平时你就只需要将自己想要上传到GitHub上的文件放到本地的仓库里面，然后只需要通过一些命令），就能将文件上传到GitHub上面去。



**上传文件部分**

10.将想要上传的文件放到本地刚新建的文件夹里面



11.先返回到刚刚创建的目录下（此目录在第7步时已经被git管理，就是本地仓库），然后输入命令**git add .**     提交文件

**git add .**



12.生成一个commit，""里面的内容可随便填写，中文也行

**git commit - m "提交纪录"**



13.利用**git push**命令，将文件推送给服务器

**git push origin master**



14.进入GitHub查看，文件已经上传到了GitHub里面了，即上传成功

