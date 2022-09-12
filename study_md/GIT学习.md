# GIT学习

标签： study

---

[TOC]

##  github查找

可以按照项目名称(name)、项目描述(description)、README(readme)、收藏数()stars

>in:name 测试开放 in:description 面试题 in:readme python stars:>50

## 常见命令

```
#查看指定文件状态
touch .gitignore #创建gitignore文件
git status [filename]

#查看所有文件状态
git status

git add .                  添加所有文件到暂存区
git commit -m "消息内容"    提交暂存区中的内容到本地仓库 -m 提交信息
git push                   上传仓库文件到远程仓库
```

## 1 前言

>git在控制台的复制粘贴 鼠标选中即是复制 单击鼠标滚轮即为粘贴
>linux在终端的复制粘贴  复制命令：Ctrl + Shift + C  粘贴命令：Ctrl + Shift + V  

---


>版本控制

版本控制（Revision control）是一种在开发的过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录，备份以便恢复以前的版本的软件工程技术。

>软件下载和安装

官网：https://git-scm.com/
淘宝镜像：http://npm.taobao.org/mirrors/git-for-windows/


Git Bash：Unix与Linux风格的命令行，使用最多，推荐最多

Git CMD：Windows风格的命令行

Git GUI：图形界面的Git，不建议初学者使用，尽量先熟悉常用命令

## 2 Linux常见命令

1. cd : 改变目录。
2. cd . . 回退到上一个目录，直接cd进入默认目录
3. pwd : 显示当前所在的目录路径。
4. ls(ll):  都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细。
5. touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件。
6. rm:  删除一个文件, rm index.js 就会把index.js文件删除。
7. mkdir:  新建一个目录,就是新建一个文件夹。
8. rm -r :  删除一个文件夹, rm -r src 删除src目录
9. pwd：展示当前所在路径


## 3 GIT配置文件

```

#查看系统config
git config --system --list
　　
#查看当前用户（global）配置
git config --global  --list

C:\Users\Administrator\ .gitconfig    只适用于当前登录用户的配置  --global 全局
可以修改账号密码


git config --global user.name "kuangshen"  #名称
git config --global user.email 24736743@qq.com   #邮箱

#建立ssh 生成自己的公钥
> 确认秘钥的保存路径（如果不需要改路径则直接回车）；
> 如果上一步置顶的保存路径下已经有秘钥文件，则需要确认是否覆盖（如果之前的秘钥不再需要则直接回车覆盖，如需要则手动拷贝到其他目录后再覆盖）；
> 创建密码（如果不需要密码则直接回车）；
> 确认密码；

ssh-keygen -t rsa -C "这里换上你的邮箱" 

#在安装路径./ssh下会生成id_rsa.pub 再配置进入github即可

```


## 4 GIT基本理论

Git本地有三个工作区域：工作目录（Working Directory）、暂存区(Stage/Index)、资源库版本库(Repository或Git Directory或History)。如果在加上远程的git仓库(Remote Directory)就可以分为四个工作区域。文件在这四个区域之间的转换关系如下

![image.png-97.5kB][1]


本地上的文件显示

![image.png-173.6kB][2]


## 5 GIT仓库的创建

![image.png-92.2kB][3]


  
- GIT仓库的创建

```
# 在当前目录新建一个Git代码库
$ git init
```

- GIT克隆

```
# 克隆一个项目和它的整个代码历史(版本信息)
$ git clone [url]  # https://gitee.com/kuangstudy/openclass.git
```

## 时光穿梭机

```
git status #查看当前缓冲区的内容
git diff    #查看当前文件和仓库提交文件的不同
git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别：

#提交
git add test.txt
git commit -m "test1"


#开始回溯
#查看日志
git log 

#输出如下
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)    #1094adb7b。。。为版本号
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL
    
#回退
git reset --hard HEAD^  #HEAD^ HEAD的上个版本 HEAD^^ 上上个版本 HEAD~100上100个版本

#回到最新的版本
git reset --hard 1094a #后面为版本号

#为了找到提交的命令
git reflog   #记录每次提交的指令
    

#从git中删除
rm test.txt #本地删除
#仓库删除
git rm test.txt 
git commit
```

## 彻底删除文件

有些时候不小心上传了一些敏感文件(例如密码), 或者不想上传的文件(没及时或忘了加到.gitignore里的),
而且上传的文件又特别大的时候, 这将导致别人clone你的代码或下载zip包的时候也必须更新或下载这些无用的文件,
因此, 我们需要一个方法, 永久的删除这些文件(包括该文件的历史记录).

### 从你的资料库里删除文件

```
 git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch path-to-your-remove-file' --prune-empty --tag-name-filter cat -- --all
 # 如果你要删除的目标不是文件，而是文件夹，那么请在 `git rm --cached' 命令后面添加 -r 命令
```

### 更新远程仓库 以覆盖的形式

```
 git push origin master --force --all
```

### 清理回收空间(.git)

```
$ rm -rf .git/refs/original/

$ git reflog expire --expire=now --all

$ git gc --prune=now

Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (1378/1378), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1461), reused 1802 (delta 1048)

$ git gc --aggressive --prune=now

Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (2426/2426), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1483), reused 0 (delta 0)
```

## 6 GIT文件的操作


### 文件的状态

- Untracked: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.

- Unmodify: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified. 如果使用git rm移出版本库, 则成为Untracked文件

- Modified: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过, 返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改 !

- Staged: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存, 文件状态为Modified

### 查看文件状态

```
#查看指定文件状态
git status [filename]

#查看所有文件状态
git status

git add .                  添加所有文件到暂存区
git commit -m "消息内容"    提交暂存区中的内容到本地仓库 -m 提交信息
git push                   上传仓库文件到远程仓库
```

### 忽略文件

在主目录建立".gitignore"文件，此文件有如下规则：
```
touch .gitignore #创建gitignore文件
#为注释
*.txt        #忽略所有 .txt结尾的文件,这样的话上传就不会被选中！
!lib.txt     #但lib.txt除外
/temp        #仅忽略项目根目录下的TODO文件,不包括其它目录temp
build/       #忽略build/目录下的所有文件
doc/*.txt    #会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

可以避免一些文件的上传

### 查看历史版本

```
git log 查看历史所有版本信息 

git log 文件名

git log -x 查看最新的x个版本信息

git log -x filename查看某个文件filename最新的x个版本信息（需要进入该文件所在目录）

git log --pretty=oneline查看历史所有版本信息，只包含版本号和记录描述

git show hash值 查看改动
```

## 7 Gitee的使用和远程仓库连接

### 设置ssh公钥 实现免密码登录

```
# 打开bash
# 生成公钥
ssh-keygen -t rsa -C "youremail@example.com" #github邮箱 接下来确定路径和密码
# 成功的话会在 ~/ 下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key。

# 连接远程仓库
# 提交到 Github
$ git remote add origin git@github.com:tianqixin/runoob-git-test.git #git@... 即仓库的ssh
$ git push -u origin master


# 提取远程仓库
git fetch #从远程仓库下载分支和数据
git merge #从远程仓库提取数据合并到当前分支
git remote -v #执行时加上 -v 参数，你还可以看到每个别名的实际链接地址。
git remote rm [别名]
```

### 许可证

许可证：开源是否可以随意转载，开源但是不能商业使用，不能转载，...  限制！

一般使用GPL-3.0

### 8 工程文件配置

>主要有两种方式

1. 将远程的git文件拷贝到项目中即可
2. 让项目文件生成在git目录中

## git分支

git中的分支即平行独立的版本
![image.png-71.8kB][4]


  [1]: http://static.zybuluo.com/ShowArcher/xqs6gdxo8szpvw2456rhjase/image.png
  [2]: http://static.zybuluo.com/ShowArcher/m1czhn2lg6fcu12cidw6vg2h/image.png
  [3]: http://static.zybuluo.com/ShowArcher/lxvk5ocn2hs8qn4lcy4n9v5i/image.png
  [4]: http://static.zybuluo.com/ShowArcher/3vmfg1y00m25ikte4qrpxuv0/image.png
  
git中的分支指令

```

# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```