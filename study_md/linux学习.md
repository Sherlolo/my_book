# linux学习

标签（空格分隔）： study

---

```
sudo bash ./rjsupplicant.sh -d 0 -u 202113704121 -p yyn1314520
sudo NetworkManager
```


# linux是什么与如何学习

## linux的基本概念

![Q]WM82@)9]GY7OU8ZFM}MZO.jpg-142.6kB](http://static.zybuluo.com/ShowArcher/rspiu1bdp6hsuwddubhyyoek/Q%5DWM82@%299%5DGY7OU8ZFM%7DMZO.jpg)

如上所示，计算机一般由应用程序、系统调用、内核、硬件组成。而系统调用和内核统称为操作系统。
linux就是操作系统，操作系统的内核必须跟硬件配合，和提供及控制硬件的资源进行良好的工作。

### UNIX（linux的前身）

UNIX是在贝尔实验室中产生的，由实验室的开发者将B语言重写成C语言编写的UNIX内核。

### GNU计划（GNU's Not Unix）

GNU计划是斯托曼发起的一个计划，该计划的目的是：建立一个自由、开放的UNIX操作系统。
在最初的开发期间都是针对UNIX进行开发的。

开发期间产生的产品：

- Emacs
- GNU C Compiler(GCC)
- GNU C Library
- Bash shell

>X Window system: UNIX上的图形用户界面

### 各类软件的分类

按使用权限的软件分类：

- 自由软件：提供源码，可以商业化提供服务
- 闭源软件/专利软件：只提供可运行的二进制程序
- 免费软件：不提供源码，但是免费使用
- 共享软件：使用初期免费，过后付费

## Linux的发展

Linux，全称GNU/Linux，是一种免费使用和自由传播的类UNIX操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年10月5日首次发布，它主要受到Minix和Unix思想的启发，是一个基于POSIX的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的Unix工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

### Linux的发行版

Linux指的是操作系统的内核，而发行版是指各厂商在内核基础上设计软件和工具等可安装程序。
发行版分类：

- RPM方式安装软件的系统：RHEL、Centos、SUSE、Fedora
- Debian方式安装dpkg的系统：Ubuntu、Debian、B2D

## Linux的应用

Linux内核小巧精致，可以在很多强调省电已经配置较低的硬件资源环境下面运行。

具体应用：

- 网络服务器
- 关键任务（硬实时系统）
- 高性能计算任务 分布式系统
- 个人桌面计算机
- 嵌入式系统
- 手机系统 android就是linux内核的分支
- 云端应用

## Linux怎么学习

掌握如下技能：

- 基础技能：用户、权限、程序的定义
- vi文本编辑器
- shell脚本
- 软件管理
- 网络基础知识：搭建网址

> 作为用户，人要迁就机器；作为开发者，要机器迁就人。

# 主机规划和磁盘分区

## 各硬件设备在linux中的文件名

|设备|文件名|
|------|----------|
|SATA、USB|/dev/sd[a-p]|
|打印机|/dev/lp[0-2] /dev/usb/lp[0-15]|

### 磁盘文件名的命名规则

设备的文件名是由检测到的顺序来决定设备文件名的，而不是插槽的顺序有关。

>注意：主要分区的命名范围：sda1-sda4 扩展分区的命名范围：sda5-sda99

## MBR和GPT磁盘分区表

MBR和GPT是磁盘分区的一种格式

## MBR

MBR分区格式中的第一个扇区包含如下：

- 主引导记录：可以安装引导程序的地方
- 分区表：记录整块硬盘分区的状态
- 分区看分为主要分区和扩展分区

### 扩展分区

现有1-400的磁盘。P1:1-100，P2:101-400。
P2为扩展分区，P2中也有各分区表，将P2分为4各逻辑分区。

### MBR分区的特点

特点：

- 主要分区和扩展分区最多可以有4个（硬盘的限制）
- 扩展分区最多只有1个（系统的限制）
- 逻辑分区是由扩展分区连续划分出来的分区
- 扩展分区无法被格式化，主要分区和逻辑分区可以。
- 如要重新划分扩展分区，需要将其破坏，这样所有逻辑分区的数据都会被删除。
- 逻辑分区的数量依操作系统不同而不同

缺点：

- 操作系统无法使用2.2T以上的磁盘容量
- MBR仅有一个区块，若被损坏，很难恢复。
- MBR内的存放引导程序仅446字节，容量较小。

## GPT

GPT将磁盘所有区块以LBA来规划。使用34个LBA记录分区信息，并将磁盘最后的34个LBA作为另一个备份。

### GPT的LAB结构

- LAB0（MBR兼容块）

存储第一阶段的启动引导程序

- LAB1(GPT表头记录)

记录了个每个分区表本身的位置和大小

- LAB2-33

每个LAB都可以记录3组分区记录


## BIOS启动检测程序(搭配MBR)

BIOS和UEFI是内置在CMOS上的程序，计算机启动时，计算机系统会主动执行的第一个程序。
计算机的启动流程如下：

- BIOS:启动主动执行的固件，会认识第一个可启动的设备
- MBR：找到MBR分区格式中的第一个扇区中的主引导记录快。
- 启动引导程序(Boot Loader)：一个可读取内核文件的执行程序
- 内核文件：开始启动操作系统

### 启动引导程序(Boot Loader)

其作用如下：

- 提供选项：用户可以选择不同的启动选项
- 加载内核文件：直接指向可使用的程序区段来启动操作系统
- 转交其他启动引导程序：将启动管理功能转交给其它启动引导程序（双系统）

>双系统

启动引导程序除了可以安装在MBR中，也可以安装每个分区的启动扇区（boot sector）
每个分区都拥有主机的启动扇区。
注意：一般先安装windows再安装linux，因为windos在安装的工程中，会主动地覆盖MBVR以及自己所在分区的启动扇区。

## UEFI启动检测程序(搭配GPT)

BIOS是汇编写的，而UEFI是c语言写的，其特点如下：

- 有GUI管理窗口
- 更加安全
- 提供安全启动功能(secure boot)，代表启动的系统需要被UEFI验证才可，很多时候需要关闭它。


## 文件系统挂载

挂载：就是利用一个目录当成进入点，将磁盘分区的数据放在该目录下。为目录分配磁盘（分区）。

## 系统分区

>最简单：只划分/和swap分区 如果一个小细节损坏，根目录可能整个损坏
>一般：划分/boot，/，/home，/var，swap

# 安装系统

## 烧机

安装完系统后可以烧机，内存压力测试。在开机引导界面：Troubleshooting->Run a memory test

# 初次了解linux系统

## X Window 和 命令行模式切换

linux有两种模式，一是带图形化窗口的X window和命令行模式。
注意：ubuntu中开机登录界面默认使用gdm3

采用如下命令切换：

- [Ctrl + Alt + F2-F6]: 命令行模式登录tty2-tty6
- [Ctrl + Alt + F1]: 图形用户界面
- [Ctrl + Alt + Backspace]:重启用户界面

> 当启动完成后，默认系统只会提供一个tty，在你切换后才会参数额外的tty2等
若在命令行模式下想启动图形窗口，需要输入[startx]才可。

## linxu系统的命令格式

    [sherlolo@study ~]$ command [-options] parameter1 parmeter2 ...

详细描述如下：

- ~代表家目录 比如~代表/home/sherlolo
- 默认root的提示字符为#，而一般用户为$
- []中代表可选的，通常选项前带-，表示缩写：-h，带--表示全写，如：--help
- 命令、选项、参数用空格进行隔断，不论几个空格
- 回车键代表开始启动
- 命令太长可以用\来转移回车键，注意反斜杠后需立刻接字符。比如ls -l 可以用ls \enter -l
- 命令中大小字母是不一样的

## 语系的支持

```
$ locale
LANG=zh_CN.UTF-8 # 语言语系的输出
LANGUAGE=zh_CN:zh:en_US:en
LC_CTYPE="zh_CN.UTF-8"
LC_NUMERIC=zh_CN.UTF-8
LC_TIME=zh_CN.UTF-8 # 时间方面的语系
LC_COLLATE="zh_CN.UTF-8"
LC_MONETARY=zh_CN.UTF-8
LC_MESSAGES="zh_CN.UTF-8"
LC_PAPER=zh_CN.UTF-8
LC_NAME=zh_CN.UTF-8
LC_ADDRESS=zh_CN.UTF-8
LC_TELEPHONE=zh_CN.UTF-8
LC_MEASUREMENT=zh_CN.UTF-8
LC_IDENTIFICATION=zh_CN.UTF-8
LC_ALL= # 全部的数据同步更新的设置值

#更改语系 只限与本次登录的结果
$ LANG=en_US.utf8
$ export LC_ALL=en_US.utf8

```

## 基础命令的操作

- 显示时间：date
- 显示日历：cal
- 简单计算器：bc
- ls -al 显示所有文件（包含隐藏） ls -l 显示所有文件（不包含隐藏）
### 具体使用

```
$ date #显示当前时间
2022年 02月 18日 星期五 11:08:02 CST
$ date +%Y/%m/%d #格式化输出的结果
2022/02/18

cal [month] [year]

#简单计算器

bc #(/ ^ % 对应除法 指数 余数)
bc中使用quit退出
```

### 注意事项

命令行里执行命令有两种模式

- 该命令直接显示结果，然后回到命令提示符等待下个命令的输入
- 进入到该命令的环境，直到结束该命令才回到提示符中

## 命令行的使用快捷键（重点）

### tab快捷键

功能如下：

- 第一个字段后面，为命令补齐
- 第二个字段后面，为文件补齐
- 安装了bash-completion，则可以进行参数补齐的功能

```
$ ca [tab] [tab]
#显示所有以ca开头的命令

$ls -al ~/.Bash [tab] [tab]
#显示以.Bash开头的文件名

$ date --[tab] [tab]
#显示date的命令参数

$ dat[tab] 
#参数补齐
```

### ctrl + c

中断目前程序

### ctrl + d

代表键盘输入结束(EOF),可用来替代exit，退出命令行模式

## linux的在线求助系统man page和info page

### --help 求助说明

    date --help

--help选项会协助你查询命令的具备的选项和参数

### man page

man page 会详细的说明命令的用法，man是manual(操作说明)的简写。

```
man date

#在进入man命令后，可以按下[空格键]向下翻页，[q]退出
```

>man date(1) 中的1代表含义如下：

|数字代号|代表内容|
|--|---------------------------------------------|
|1|用户在 shell 环境中可以操作的命令或可执行文件|
|2|系统内核可调用的函数与工具等|
|3|	一些常用的函数与函数库，大部分为 C 的函数库|
|4|	设备文件的说明，通常在 /dev 下的文件|
|5|	配置文件或者是某些文件的格式|
|6|	游戏|
|7|	惯例与协议等，例如 Linux 文件系统、网络协议、ASCII code 等说明|
|8|	系统管理员可用的管理命令|
|9|	和 Kernel 有关的文件|


>man page的内容部分：

|代号|	内容说明|
|------|---------------------|
|NAME|	简短的命令，数据名称说明
|SYNOPSIS|	简短的命令执行语法简介
|DESCRIPTION|	较为完整的说明
|OPTIONS|	针对 SYNOPSIS 部分中，有列举的所有可用的选项说明
|COMMANDS|	当这个程序在执行的时候，可以在此程序中执行的命令
|FILES|	这个程序或数据所使用或参考或连接到的某些文件
|SEE ALSO|	这个命令或数据有相关的其他说明
|EXAMPLE|	一些可以参考的范例
|BUGS|	是否有相关的错误


>建议查询 man page 时的步骤：

1. 先查看 NAME 的项目

2. 仔细看一下 DESCRIPTION， 学习一些细节

3. 查询关于 OPTIONS 的部分，了解每个选项的意义

4. 查看 SEE ALSO 来看一下还有那些东西可以使用

5. 查看 FILES 部分的文件来参考

>man page页面的采用快捷键

|按键|功能|
|-----|-----------------|
|空格|	向下翻一页
|[Page Down]|	向下翻一页
|[Page Up]	|向上翻一页
|[Home]	|去到第一页
|[End]|	去到最后一页
|/string|	向下查询string字符串
|?string|	向上查询string字符串
|n, N	|利用 / 或 ? 来查询字符串时，可以用 n 来继续下一个查询，用 N 来进行反向查询
|q	|结束 man page 环境

>查找命令

```
man -f 命令：查找与命令完全一样的名称
man -k 命令: 将文件内容中有命令关键词的列出
```

案列如下：
```
(base) sherlolo@sherlolo:~$ man -f ls
ls (1)               - list directory contents

(base) sherlolo@sherlolo:~$ man -k sudo
cvtsudoers (1)       - convert between sudoers file formats
gnome-sudoku (6)     - puzzle game for the popular Japanese sudoku logic puzzle
sudo (8)             - execute a command as another user
sudo.conf (5)        - configuration for sudo front end
sudo_plugin (8)      - Sudo Plugin API
sudo_root (8)        - How to run administrative commands
sudoedit (8)         - execute a command as another user
sudoers (5)          - default sudo security policy plugin
sudoers_timestamp (5) - Sudoers Time Stamp Format
sudoreplay (8)       - replay sudo session logs
visudo (8)           - edit the sudoers file
```

## info page

info page与man page类似，但info page是将文件数据变成各个独立的页面。

info page是只有linux才有的产物，并且易读性强很多。

## 系统关机

linux是多人多任务系统，所以用电源键关机，可能造成文件系统的损坏。
windows是单人多任务

### 查看系统状态

```
who #查看有谁在线
netstat -a #查看网络的联机状态
ps -aux #查看后台执行任务
```

### 常见的关机命令

将数据同步写入C盘：sync
关机命令：shutdown
重新启动、关机：reboot、halt、poweroff

sync会将内存中暂存的数据存放到磁盘中去，现在的shutdown、reboot、poweroff在关机前都会执行sync指令


### shutdown

```
shutdown [-krhc] [时间] [警告信息]
-k :不是真的关机 只是发出警告
-r :系统服务停掉后重新启动
-h :系统服务停掉后立即关机
-c :取消正在进行的shutdown命令
时间：指点关机时间


shutdown -h 10 #10分钟后关机
shutdown -h 20:25 #20:25关机

### reboot、halt、poweroff

```

halt: 系统停止 屏幕可能会保留系统已经停止的信息， 电源任保留
poweroff：系统关机，屏幕空白。
reboot:重新启动
suspend:进入休眠模式

>注意所有的关机命令都是通过调用systemctl这个管理命令来进行的


# linux的文件权限和目录配置

## 文件的三种权限

一般文件有三种身份的权限：拥有者、所属群组、其他人。还有一个特殊的root，管理员身份，可以访问一切。

>案列：简单来说有王大毛一家，有三个成员王大毛，王二毛，王三毛。他们有各自独立的空间，也有一个共享的空间。对每个成员的独立空间来说，其拥有者为个人，王大毛家为一个群组，其他人来访问则为其他人身份

>所有系统上的账号和一般身份用户都记录在/etc/password这个文件上，个人的密码在etc/shadow,
组名则是在/etc/group上

### linux文件属性

![image.png-36.7kB](http://static.zybuluo.com/ShowArcher/3ptzh5q55n3a3xrf3pscm13k/image.png)

```
执行ls -al
-rwxr-xr-x 1 root root 7 04-21 12:47 test.txt  
```

- 第一栏

>第一个属性，表示这个文件的类型，常见的有：文件、目录或连接文件等。
" d "：   表示是一个 目录（directory）；
" - "：   表示一个 文件；
" l "：    表示一个连接文件（link file）；  
后九个属性中，每三个位一组，"r"表示可读（read）、"w"表示可写（write）、"x"表示可执行（excute）。
第一组为“拥有者owner的权限”；
第二组为“同用户组的权限”；
第三组为“其他人的权限”；

-   第二列表示链接占用的节点，这个主要是和link node有关，初学linux的可以先不用研究。 

-   第三列表示文件的“拥有者”，即owner。 

-   第四列表示拥有者的“用户组”。 

-   第五列表示这个文件的大小。 

-   第六列表示文件的最后“修改时间”（即modification time, 简称mtime），对于新创建的文件就是指其创建的时间。     

>补充：linux系统“文件时间”主要包括三个内容：
修改时间（modification time, 简称mtime）：当前文件“内容数据”更改时，这个属性被更新。使用ls命令显示的时间就是“修改时间mtime ”。
状态时间（status time, 简称ctime）：当文件状态(status)改变时，这个属性被更新。例如：更新文件的权限和属性时。
访问时间（access time, 简称atime）：当读取文件内容时，这个属性被更新。
注意：如果要显示完整的时间格式 ls -l --full-time
如果只是更改文件的内容，“状态时间ctime” 会改变，
但是“修改时间mtime”是不会改变，因为文件的内容数据并没有变化。

-  第七列就是文件的文件名。注意：在linux系统中，如果一个文件名以"."开头，那么这个文件就是隐藏文件，这点与windows不同。 

## 修改文件属性和权限

主要利用如下三个命令：

- chgrp: 修改文件所属用户组 change group
- chown: 修改文件拥有者 change owner
- chmod：修改文件权限 change mod

## 修改文件所属用户组和文件拥有者

注意如果要修改，必须用户或用户组在password和group中

```
chgrp [-R] 账号名称 文件或目录
chown [-R] 账号名称 文件或目录
chown [-R] 账号名称:文件或目录
-R: 连同子目录下的所有文件都将被修改

案例：
chgrp users init.cfg
chown bin initial.cfg
```

## 修改权限 chmod

修改文件权限有两种方式，分别是数字和符号来改写。

### 数字类型修改

数字与权限对应关系如下：

- r: 4
- w: 2
- x: 1

```
rwx = 4+2+1 = 7
--- = 0+0+0 = 0

chmod [-R] xyz文件或目录 （xyz为所属权限之和）

chmod 777 .bashrc (777对应拥有者、用户、其他者的权限)
```

### 符号类型的修改文件权限

![image.png-13.7kB](http://static.zybuluo.com/ShowArcher/22um96bbilqfo2loc26i00md/image.png)

u: user, g:group, o:other, a:all

```
案例：

chmod u=rwx,go=rx .bashrc
chmod a-x .bashrc #所有身份去掉执行权限
chmod a+x .bashrc
chmod a+w .bashrc

```

## 目录与文件的权限意义

### 权限对文件的意义

- r(read): 读取此文件的实际内容
- w(write):可以编辑、新增或修改文件的内容（但不包含删除文件的权限）
- x(execute): 该文件具有可以被系统执行的权限，只是代表可以被执行的能力，能否成功还是要看文件的内容

### 权限对目录的意义

- r：可以查询该目录下的文件名数据，可以通过ls显示出来,但不能进入
- w: 具有改动该目录结构列表的权限
>包含： 建立新的文件与目录
删除已经存在的文件与目录(无论该文件的权限是什么)
对已存在的文件或目录更改名字
移动该目录下的文件、目录位置
- x： 代表用户能否进入该目录成为工作目录的用途


>注意：
如果你在某目录下没有x的权限，那么你就无法切换到该目录下执行各种命令，即使你具备r或w的权限
如果你在某目录有rwx的权限，即使你对该目录下的文件没有任何权限，你也可以删除它。
如果没有r权限，使用[tab]将无法补全而已。


## linux的文件种类和扩展名

### 文件种类

可以大致分为如下几种：

- 常规文件 （一般进行读写的类型文件 第一个字符为-）
>详细可分为：
纯文本文件(ASCII):人类可以直接读到的数据，如数字、字母。可用cat命令读出
二进制文件(binary):计算机可以识别的可执行文件
数据文件(data)：程序运行过程中产生的数据文件，是一种特殊格式，可以last读，并不能用cat
- 目录文件 （第一个属性为d）
- 链接文件 (-l 类似与快键方式)
- 设备与设备文件 (device)
>与系统周边和存储有关的一些文件，同在在/dev目录下
区块设备文件：提供系统随机存取的接口文件，磁盘、软盘
字符设备文件： 一些串行端口的接口设备。键盘、鼠标
- 数据接口文件(sockets 常用于网络上的数据交换)
- 数据传输文件(FIFO pipe)
> FIFO先进先出，即管道，为了解决多个程序同时读写一个文件的错误问题。

## linux的目录配置

linux针对与系统开发提出了Filesystem Hierarchy Standard(FHS)，主要目的是让用户了解到已安装软件通常放在哪个目录下。

### FHS

FHS将目录定义成四种形态：

- 可分享: 能够分享给网络上其他主机挂载用的目录
- 不可分享: 不能分享给其他主机
- 不变：不会经常变动、跟随着发行版而不变态
- 可变动：经常修改的数据，比如日志文件
-

FHS定义了三层目录下应该放置什么数据：

- /(root),根目录：与启动系统有关
- /usr:与软件安装/执行有关
- /var:与系统运行有关

详细目录配置如下：

![image.png-185.2kB](http://static.zybuluo.com/ShowArcher/c5k3gdhgsjcp5vteoxaiklxe/image.png)

![image.png-234.7kB](http://static.zybuluo.com/ShowArcher/vrcm8l6hqkfmayjy80kxt10x/image.png)

![image.png-188.6kB](http://static.zybuluo.com/ShowArcher/gjmjcby8sx6bbs2glnwsbef8/image.png)

![image.png-67.9kB](http://static.zybuluo.com/ShowArcher/69o05ayzjg7j1cs4ogmhogid/image.png)


最终的目录树结构如下：

![image.png-319.9kB](http://static.zybuluo.com/ShowArcher/i90sbsia520ja2gagfiwhs6t/image.png)


# 目录与路径

## 目录的有关操作

如下比较特殊的目录：

```
. 代表此层目录
.. 代表上一层目录
- 代表前一个工作目录
~ 代表目前使用者身份的家目录
~account 代表account这个使用者的家目录
```

### 关于处理目录的指令

- cd
> cd:切换目录 change directory
cd - ，cd ~

- pwd
>pwd:显示目前所在目录 print working directory
pwd [-P] -P代表显示真正的路径而不是链接的路径。没有会显示链接的路径

- mkdir
>mkdir:建立新的目录 make directory
mkdir [-mp] -m:设置文件的权限 -p:帮助你将所需的目录递归创建
mkdri -p test/test1

- rmdir
>rmdir:删除空目录 remove directory
rmdir [-p] 目录名称 -p 连同上层空的目录一起删除
rm -r test: 删除不为空的目录 r也带边递归


### 关于执行文件路径的变量 $PATH (环境变量)

在linux中$PATH($代表后面接的是变量)代表环境变量，在执行命令时，会在PATH文件名查找可以运行的命令。
PATH里面由一堆目录组成，每个目录中间用冒号隔开。

案例：

```
echo $PATH #查看环境变量
./ls #如果要使用当前目录下的命令，必须使用相对路径或绝对路径。PATH里不包含(.当前目录)
PATH="${PATH}:/root" #将/root加入到path中

```

PATH由如下特性：

- 不同身份用户默认的PATH不同，能够执行的命令也就不同
- PATH是可以修改的
- 当前目录一般不放在PATH中

## 文件和目录管理

### 复制、删除、移动

- cp(copy):复制文件，建立链接方式
- mv(move):移动目录文件，也可进行重命名
- rm(remove):删除

#### cp

    cp -adfilprsu 源文件 目标文件

- a: 相当于-dr --preserve=all的意思 即复制所有的属性
- d: 若源文件为链接文件，则复制链接文件属性而非文件本身
- i: 若目标文件已存在时，在覆盖时会先询问操作的进行
- l: 进行硬链接的链接文件建立。而非文件本身
- s: 复制成符号链接文件，即快键方式。
- p: 连同文件的属性一切复制过去，而非使用默认属性
- r：递归复制，用于目录的复制

cp复制后，目标文件的拥有者通常是命令操作本身
所以在复制时应考虑如下：

- 是否需要完整的保留源文件信息
- 源文件是否为符号链接文件
- 源文件是否为特殊的文件
- 源文件是否为目录

#### rm(删除文件或目录)

    rm [-fir] 文件或目录

选项和参数：

- f: force 强制的意思
- I/i: 交互模式，删除前进行询问
- r：递归删除

#### mv (移动文件或目录 重命名)

    mv [-fiu] 源文件 目标文件

选项和参数：

- f: force 强制的意思, 若目标文件已存在会直接覆盖
- I/i: 交互模式，删除前进行询问
- u：若目标文件已存在，且源文件比较新，才会更新

### 文件名和目录名称(basename dirname)

```
\etc\sysconfig\network

basename: network 文件名
dirname: \etc\sysconfig\ 前面的路径
```

## 文件内容查看

各个命令的用途：

- cat: (concatnate串联)由第一行开始显示文件内容 tab以^I表示 换行符以$表示
- tac：从最后一行开始显示文件内容
- nl： 显示的时候，同时输出行号
- more：一页一页地显示文件内容
- less：less与more类似，但可以往前翻页
- head：前几行
- tail：只看后几行
- od： 二进制方式读取文件内容

## 修改文件时间或创建文件touch

每个文件都会记录三个时间：

- 修改时间(mtime)：文件的内容改变时，就会更新这个时间
- 状态时间(ctime)：文件的状态(权限和属性)改变时，更新时间
- 读取时间(atime)：文件的内容被读取时，比如用cat读取后，就会更新时间

### touch指令

touch指令用于修改文件时间，或者建立一个空的文件夹

    touch [-acdmt] 文件

- a: 仅自定义的access time
- c: 仅修改文件的时间，文件不存在则建立一个新文件
- d: 后面可以接自定义的日期
- m：仅修改mtime
- t：可以接自定义的时间，格式为[YYYYMMhhmm]

>注意
ll是ls -l的命令别名
;代表连续命令的执行

## 文件和目录的默认权限和隐藏权限

### 默认权限umask

umask就是指定目前用户在建立文件或目录时的默认权限值

```
# umask
0022 #第一组为特殊权限，后面三组为一般权限
# umask -S
u=rwx, g=rx, o=rx
```

注意的是，umask的数字指默认值要减掉的权限

 - 文件默认没有x权限，最大为666(r4w2x1)
 - 目录则有x权限，最大为777
 - 文件666-003 = 664 (rw- - -wx = r--)

### 文件隐藏属性

文件隐藏属性在系统安全上有非常大的重要性。

- chattr（配置文件隐藏属性）

```
chattr [+-=] [ASacdistu] 文件或目录名称 (只能在传统的ext2、ext3的传统文件系统上完整生效)
+-=: 添加、删除、设置属性
a : 设置属性a后，文件只能增加数据，不能删除也不能修改数据
i : 让一个文件不能被删除，改名、写入删除文件

xfs文件仅支持Aadis属性

案例：
touch attrest
chattr +i attrest
rm attrest #无法删除
chattr -i attrest
```

- lsattr（显示文件隐藏属性）

```
lsattr [-adR] 文件或目录
-a :将隐藏文件的属性也显示出来
-d :只显示文件夹的属性而不是文件夹里的内容属性
-r :连同子目录的属性一起显示出来
```

### 文件特殊属性 (SUID、SGID、SBIT)

set UID 当s这个标志出现在文件拥有者的x权限上时

- SUID权限仅对二进制程序有效
- 执行者需要对该程序具有x的可执行权限
- 本权限仅在该程序的过程中有效 执行者将具有该程序拥有者的权限

Set GID SGID可以针对文件或目录来设置

- SGID对二进制程序有用
- 程序执行者对该程序来说，需具备x的权限
- 执行在执行过程将获得该程序用户组的支持

Sticky bit 只针对目录有效:

- 用户需度目录具有rx权限
- 当用户在该目录建立文件或目录时，仅自己与root有权力删除

### 观察文件类型 (file)

file 文件可以查看文件的格式，SUID权限，兼容的平台，动态链接库。

```
# file ~/.bashrc 
/root/.bashrc: ASCII text
# file /usr/bin/passwd
/usr/bin/passwd: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=6af93256cb810d90b2f96fc052b05b43b954f5b2, for GNU/Linux 3.2.0, stripped #使用的是linux内核3.2.0
```

## 命令与文件的查找

命令行模式中，可以连续输入两次[tab]知道用户有多少命令可以执行
which或type可以看到这些命令放在哪儿

### 脚本文件的查找 which

```
which [-a] command 
#该命令是根据[PATH]这个环境变量所规范的路径，去查找执行文件的文件名，若加上-a选项，则可以列出所有可以找的执行文件


# which ifconfig
/usr/sbin/ifconfig

# which -a ifconfig
/usr/sbin/ifconfig
/sbin/ifconfig

```

## 文件的查找

一般文件的查找，会优先使用whereis或locate，最后才是使用find，因为whereis只在特定目录下查找，locate则是利用数据来查找文件名，都相对比较快速

### whereis(在一些特定的目录中查找文件)

    whereis [-bmsu] 文件或目录名

参数介绍：

- l: 只列出whereis查询的几个主要目录
- b：只查找二进制文件
- m: 只在说明文件manual路径下查找
- s: 只找source源文件
- u: 查找不在上面三个项目的其他特殊文件

### locate / updatedb

locate 会在已建立的数据库/var/lib/mlocate里面查找，找出用户输入关键词的文件名
updatedb:更新/var/lib/mlocate内的数据库文件


    locate [-ir] keyword

参数说明：

- i: 忽略大小写的差异
- c：不输出文件名，输出查找的个数
- l：输出几行的意思
- S: 输出locate使用的数据库文件
- r: 后面可接正则表达式

### find

```
    find [PATH] [option] [action]
    
find / -name passwd #查找文件名为name的文件
find / -name "*passwd*" #查找文件名包含name关键词的文件
```


与时间有关的参数，可以是-atime, -ctime, -mtime：

- mtime n: 特指n天前(一天之内内)被修改的文件
- mtime +n: 列出在n天之前被修改的文件
- mtime -n: 列出在n天之内被修改的文件

与使用者有关的参数：

-uid n: n为使用者的账号ID
-gid n: n为用户组的ID
- user name: name
- group name: name
- nouser
- nogroup

与文件权限和名称有关的参数

- name : name
- size [+-] size: 查找比size大或小的文件
- type： type

# linux文件系统

文件系统是建立在磁盘上面的，文件系统即文件的管理系统，偏向上层的管理系统。
win98以前的微软操作系统是FAT，win2000后使用的是NTFS。U盘也一般使用FAT
Linux的文件系统为ext2(linux second extended file system)
磁盘分区表有两种形式：MBR、GPT

## 文件系统特性

>物理磁盘和虚拟磁盘

- 物理磁盘 /dev/sd[a-p][1-128]
- 虚拟磁盘 /dev/vd[a-d][1-128]

现在一个分区可以被格式化为多个文件系统，所以一个可被挂载的数据为一个文件系统而不是分区。

### 文件系统组成

文件系统一般由超级区块、inode、数据区块组成：

- 超级区块: 记录此文件系统的整体信息，包含inode和数据区块的总量、使用量以及文件系统的格式和相关信息
- inode: 记录文件的属性，一个文件占用一个inode
- 数据区块：实际记录文件的内容，若文件太大，会占用多个区块

### 文件系统的存取方法

ext2是索引式文件系统，U盘使用的是FAT格式，采用的是链表式，即1->7->4->16类似的形式

## linux的ext2文件系统

ext2文件系统一开始就将inode和数据区块规划好了，除非重新格式化。

### 数据区块

数据区块是放置文件数据的地方，在ext2中支持的区块大小有1k、2k、4k三种

另外数据区块的基本限制如下：

- 原则上，区块大小与数量在格式完后就不能再更改
- 每个区块内最多只能放置一个文件的数据
- 如果文件大于区块的大小，则文件会占用多个区块
- 如果文件小于区块的大小，则文件会完全占用该区块，该区块剩余容量不能使用


### inode table

inode一般记录的数据如下：

- 文件的读写属性(rwx)
- 文件的拥有者和用户组
- 文件的大小
- 文件建立或状态改变时间
- 文件特性的标识(UID)

inode可以采取直接、间接的形式来记录编号。这样可以大幅度提取存取的编号数

### superblock（超级区块）

超级区块是记录整个文件系统相关信息的地方

它记录的信息有：

- 数据区块和inode的总量
- 未使用和已使用的inode和区块数量
- 文件系统的挂载时间
- 文件系统挂载状态值 0为已挂载 1位未挂载

### dumpe2fs 查询ext系列的超级区块信息的命令

```
dumpe2fs [-bh] 设备文件名
-b: 仅列出坏道信息
-h: 仅列出superblock的数据

blkid #列出目前系统被格式化的设备
df -h 可以查看分区挂载位置及设备

```
## 文件系统的功能

当我们建立一个目录时，文件系统会分配一个inode和至少一个区块给目录

```
ls -i #可以查看占用的inode号码
```

### 日志文件系统

超级区块、inode对照表、区块对照表为元数据，每次新增、删除、编辑都会影响这三个数据

为了防止写入的数据在刚更新完对照表和数据区块，还没有更新超级区块，引入日志文件。
日志文件会专门记录写入或修改文件的步骤。

### 文件系统的运行

当系统的一个文件加载到内存中，如果内存中的数据没有被修改则是clean，如果修改了则是dirty
文件系统会将常用的数据防止内存中：

- 系统会将常用的文件数据放在内存的缓冲区，加速文件系统的读写
- 可以手动使用sync强制dirty的文件回写到磁盘中
- 正常关机会调用sync进行回写，如果没有正常关机，则会花时间进行磁盘检验

### 挂载点

磁盘分区划分为多个文件系统后，将文件系统和目录树结合的操作我们称为挂载。
挂载点一定是目录，该目录为进入文件系统的入口。

### VFS 识别各种文件系统的内核功能

## 文件系统的操作

### 查看磁盘和目录容量

>df查看文件系统的整体磁盘使用量,主要针对的是整个文件系统，读取超级区块的信息。 

```
df [-ahikHtm] [目录或文件名] #查看文件系统的整体磁盘使用量

- a: 列出所有的文件系统
- h: 以Gbytes、Mbytes等格式自行输出
- i: 不用磁盘容量，以inode的数量显示

/proc: 该挂载点可能都为0，因为它挂载在内存中，所以没有占用任何磁盘空间
/dev/shm/: 是利用内存虚拟出来的磁盘空间，其大小是动态变化的
```

>du查看文件系统的磁盘使用量，常用在查看目录占用的磁盘空间
默认会输出当前所有目录的容量(不包含文件)，逐步累积的过程。du

```
du [-ahskm] [目录或文件名]

-s: 仅列出总量， 而不列出个别的目录占用量，即直接计算当前目录占用量
-k: 以Kbtes列出总量显示

du -sh * #查看d
```


### 硬链接

通过文件系统的inode产生新的文件名，而不产生新文件，这种称为新链接。
硬链接只是在某个目录下新增一条文件名链接到inode号码的记录。
可以多个文件名对应到同一个inode。硬链接文件的所有属性都是一样的。

```
文件读取过程：文件名->inode->文件内容

ln [-sf] 源文件 目标文件

-s: 添加后是符号链接，不添加是硬链接
-f: 强制链接，如果目标文件存在，先删除再链接。

```

特点：

- 不能跨文件系统
- 不能链接目录
- 要完整删除一个文件必须将所有硬链接和源文件删除。
- 改变其中一文件内容 其他所有的文件都会改变。

### 软连接

软连接就是建立一个独立的文件，而这个文件会让数据的读取只限它链接那个文件的文件名。
软连接的使用更广

特点：

- 建立软连接时不要使用相对路径
- 删除软连接文件不会影响源文件，因为删除的只是软连接文件名
- 进入软连接目录，删除文件会删除原链接目录的文件。
- 改变其中一文件内容 其他所有的文件都会改变。
- 源文件删除后，软连接文件就不能访问了。
  


## 磁盘的分区、格式化、检验和挂载

磁盘分区(partition)、格式化(format)

### 查看磁盘分区状态

> lsblk 列出系统上所有的磁盘列表

```
lsblk [-dfimpt] [device]

sda           8:0    0   477G  0 disk 
├─sda1        8:1    0     1K  0 part 
├─sda2        8:2    0   1.9G  0 part /boot
├─sda3        8:3    0  14.9G  0 part [SWAP]
├─sda4        8:4    0 422.9G  0 part /home
└─sda5        8:5    0  37.3G  0 part /

```

其中的各类信息：

- NAME：设备文件名
- MAJ:MIN: 内核识别的设备通过这两个代码实现，分别是主要设备和次要设备
- RM：是否为可卸载设备
- SIZE：容量
- RO：是否为只读设备
- TYPE：磁盘(disk)、分区(partition)、只读存储器(rom)、伪设备(loop)
- MOUNTPOINT: 挂载点

> blkid列出设备的UUID等参数, UUID（全区唯一标识符）。

```
    blkid
    uuid=...
```

>parted 列出磁盘的分区表类型和分区信息

```
parted /dev/vda print

(base) sherlolo@sherlolo:~$ sudo parted /dev/sda print
型号：ATA Maxsun 512GB SSD (scsi)
磁盘 /dev/sda: 512GB
扇区大小 (逻辑/物理)：512B/512B
分区表：msdos # msdos就是MBR分区表 GPT是GPT分区表
磁盘标志：

编号  起始点  结束点  大小    类型      文件系统        标志
 1    1048kB  40.0GB  40.0GB  extended
 5    1049kB  40.0GB  40.0GB  logical   ext4
 2    40.0GB  42.0GB  2000MB  primary   ext4
 3    42.0GB  58.0GB  16.0GB  primary   linux-swap(v1)
 4    58.0GB  512GB   454GB   primary   ext4


```


## 磁盘分区

MBR分区表使用fdisk分区， GPT分区使用gdisk分区。

```
gdisk/fdisk 设备名称 #进入分区管理界面

Last sector:表示旋转分区的容量，默认会分配完全

注意：磁盘分区针对的是整个磁盘设备而不是某个分区，所以执行 gdisk dev/sda而不是sda1
```

### partprobe更新linux内核的分区表信息

注意：如果要删除一个/dev/sda2的话，必须要先将/dev/sda2卸载。所以不要处理正在活动的文件系统。


### 磁盘格式化（创建文件系统）

磁盘格式化其实就是在创建一个文件系统，所以使用mkfs。

```
mkfs 快速格式化xfs文件系统
mkfs.ext4 格式化ext4文件系统
```

## 文件系统检验

### xfs_repair处理xfs文件系统

当xfs文件系统错乱时，才会使用这个命令

### fsck.ext4处理ext4文件系统

    fsck.ext4 [-pf] [-b 超级区块] 设备名称

常用来修复启动引导

## 文件系统的挂载与卸载

挂载注意事项：

- 单一文件系统不应该被重复挂载在不同的目录中
- 单一目录不应该重复挂载多个文件系统
- 要作为挂载点的目录，理论上应该是空目录才行

### mount挂载

```
mount [-t 文件系统] LABEL='' 挂载点
mount [-t 文件系统] UUID='' 挂载点

```

### umount（将设备文件卸载）

卸载：就是将已挂载的文件系统卸载，卸载后可以使用df或mount查看是否在目录中

```
umount [-fn] 设备文件名或挂载点

-f: 强制挂载
-l: 立刻卸载文件系统
-n: 不更新 /etc/mtab情况下卸载
```

## 设置启动挂载

### /etc/fstab以及/etc/mtab

启动的时候会自动将文件系统挂载好
/etc/fstab就是我们利用mount命令挂载时，将所有的选项和参数写入的文件。并且还有dump备份用命令的支持。

详细信息：

- 第四栏：文件系统参数
- 第五栏：能否被dump备份命令作用
- 第六栏：能否以fsck检验扇区

## 特殊设备loop挂载

如果有光盘镜像文件或使用文件作为磁盘时，可以使用特殊的方法将其挂载，而不需要刻录。

```
mount -o loop DVD.iso /data/centos_dvd #挂载后可以在/data/centos_dvd里看到各种数据
```

# 文件和文件系统的压缩

## 常见的压缩命令和格式

linux支持的压缩命令和格式如下：

|文件格式|压缩程序|
|----|-------|
|.Z|compresss程序压缩的文件|
|.zip|zip程序压缩的文件|
|.gz|gzip程序压缩的文件|
|.bz2|bzip2程序压缩的文件|
|.xz|xz程序压缩的文件|
|.tar|tar程序打包的文件，并没有压缩|
|.tar.gz|tar程序打包的文件，并经过gz压缩|
|.tar.bz2|tar程序打包的文件，并经过bz压缩|
|.tar.xz|tar程序打包的文件，并经过xz压缩|

tar是将很多文件打包成为一个文件，甚至目录也可以这样打包。常见的是gzip、bzip2、xz。而compress已经不流行了。
压缩效率：gzip < bzip2 < xz
压缩时间: gzip > bzip2 > xz

注意：在压缩完成时，源文件会不复存在。

### gzip

gzip是一个较为常见的命令，其语法如下：

    gzip [-cdtv#] 文件名

参数介绍：

- c: 将压缩的数据输出到屏幕上，可以通过从定向来处理 
- d: 解压缩的参数
- t: 可以用来检验一个压缩文件的一致性，看看文件是否有损失
- v: 可以显示原文件/压缩文件的压缩比等信息
- #: 代表压缩等级， 1：压缩最快，压缩比最差， 9：压缩最慢，压缩比最好。 默认为6

### 压缩文件读取(zcat zmore zless)

cat/more/less可以用不同的方式读取纯文本文件，类似的zcat/zmore/zless则可以用对应的方式读取压缩后的压缩文件。

```
gzip -v test #压缩
gzip -d test #解压
zcat 文件名
gzip -c test > test.gz #让输出到屏幕的数据重定向到文件中，可以修改保存的文件名,并会保留源文件

zgrep -n 'http' test.gz #zgrep是grep针对压缩文件的版本，可以查找压缩文件中的内容
```

### 压缩文件 bzip2

bzip2的使用方式与gzip类似，更为常用

    bzip2 [-cdkzv#] 文件名
    bzcat 文件名.zc2

参数介绍：

- k: 保留原始文件不会删除文件
- z: 压缩的参数
- other: 其他与gzip一致

### 压缩文件 xz

同样xz的用法也类似：

    xz [-dtlkc#] 文件名
    xzcat 文件名.xz

参数介绍：

- l: 列出压缩文件的相关信息
- k: 保持原有的文件不删除

## 打包命令 tar

tar可以将多个文件或目录打包成一个大文件，也可以同时将文件打包压缩

命令：

    tar [-z|-j|-J] [cv] [-f 待建立的新文件名.tar.xz] filename....  #打包压缩 将filename压缩到新文件名
    tar [-z|-j|-J] [tv] [-f 已有的tar文件名]                #查看文件名
    tar [-z|-j|-J] [xv] [-f 已有的tar文件名]  [-c 目录]     #解压缩

参数介绍：

- c: 建立打包文件 搭配-v查看被打包的文件名
- t: 查看打包文件的内容
- x: 解压文件。
- ctx三者只能出现7一个。
- z|j|J：分别对应gzip、bzip、xz三种方式的压缩和解压
- f: 接要处理的文件名
- C：接解压的目录
- p: 保留备份数据的基本权限和属性
- P: 保留绝对路径，即保留根目录。

# vi和vim编辑器

vim是vi的升级版，vi是文本处理软件，vim是程序开发工具。一般系统已将vi设置为vim的别名
vi有三种模式：一般命令模式，编辑模式，命令行模式，其特征如下：

- 一般命令模式：光标的移动、复制、删除、粘贴。
- 编辑模式：编辑文件的内容。输入[i,l,o,O,A,r,R]进入
- 命令行模式:读取/存储文件。输入[:,/,?]进入

## vi的语法

### 一般命令模式


|按键|功能|
|--------|----------------|
|[Ctrl] + [f]|屏幕『向下』移动一页，相当于 [Page Down]按键(常用)|
|[Ctrl] + [b]|屏幕『向上』移动一页，相当于 [Page Up] 按键(常用)|
|G|	移动到这个档案的最后一行(常用)|
|nG|n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu)|
|x, X|	在一行字当中，x 为向后删除一个字符 (相当于 [del] 按键)， X 为向前删除一个字符(相当于 [backspace] 亦即是退格键)(常用)|
|nx|	n 为数字，连续向后删除 n 个字符。举例来说，我要连续删除 10 个字符，『10x』。|
|dd|	删除游标所在的那一整列(常用)|
|ndd|	n 为数字。删除光标所在的向下 n 列，例如 20dd 则是删除 20 列(常用)|
|yy|	复制游标所在的那一行(常用)|
|p, P|	p 为将已复制的数据在光标下一行贴上，P 则为贴在游标上一行！举例来说，我目前光标在第 20 行，且已经复制了 10 行数据。则按下 p 后，那 10 行数据会贴在原本的 20 行之后，亦即由 21 行开始贴。但如果是按下 P 呢？那么原本的第 20 行会被推到变成 30 行。(常用)|
|u|	复原前一个动作。(常用)|
|[Ctrl]+r|	重做上一个动作。(常用)|

### 编辑模式

|按键|功能|
|--------|----------------|
|i, I	|进入插入模式(Insert mode)：i 为『从目前光标所在处插入』， I 为『在目前所在行的第一个非空格符处开始插入』。(常用)|
|a, A	|进入插入模式(Insert mode)：a 为『从目前光标所在的下一个字符处开始插入』， A为『从光标所在行的最后一个字符处开始插入』。(常用)|
|o, O	| 进入插入模式(Insert mode)：这是英文字母 o 的大小写。o 为『在目前光标所在的下一行处插入新的一行』；O为在目前光标所在处的上一行插入新的一行！(常用)|
|r, R|	进入取代模式(Replace mode)：r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下ESC 为止；|
|[Esc]	|退出编辑模式，回到一般模式中(常用)|

上面这些按键中，在 vi 画面的左下角处会出现『--INSERT--』或『--REPLACE--』的字样。由名称就知道该动作了吧！！特别注意的是，我们上面也提过了，你想要在档案里面输入字符时，一定要在左下角处看到 INSERT 或 REPLACE 才能输入喔！


### 命令行模式

|按键|功能|
|--------|----------------|
|:w	|将编辑的数据写入硬盘档案中(常用)|
|:w!|	若文件属性为『只读』时，强制写入该档案。不过，到底能不能写入，还是跟你对该档案的档案权限有关啊！|
|:q	|离开 vi (常用)|
|:q!|	若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。|
|:wq|	储存后离开，若为 :wq! 则为强制储存后离开(常用)|
注意一下啊，那个惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～|

### 简单总结使用

![image.png-175.2kB](http://static.zybuluo.com/ShowArcher/dho0gpb5eqob0hj5cj2n8jla/image.png)


# 认识bash

## shell和内核、硬件

内核：控制这硬件工作的大小，包含cpu调度、内存管理、磁盘输入输出等功能
shell: 壳程序：用户必须通过Shell将我们输入的命令和内核沟通，好让内核可以控制硬件来正确无误的工作

## 常见的shell

linux默认使用的是[Bourne Again SHell(简称bash)]。
而windows的shell为cmd，后升级为powershell。

## 使用bash

### 查询命令是否为bash的内置命令(type)

```
type [-tpa] name

-t: 显示输出信息： file表示为外部命令，alias表示为命令别名，builtin表示为bash内置的命令功能
-p: 如果name为外部命令，才会显示完整的文件名
-a: 会有path变量定义的路径中，将s'you
```

### 多条命令的执行

如果想另起一行继续编辑命令，可以加入\enter后继续输入

## shell的变量功能

### 变量的显示

利用echo功能

```
echo $HOME 或是 echo ${HOME} #最好使用后者
```

### 变量的设置规则

变量的使用规则如下：

- 赋值： myname=vbird(等号两边不能有空格)
- 双引号可以保有原本的特性 var="Lang is $LANG" => lang is zh_CN
- 单引号则不会进行转换变量 var="Lang is $LANG" => Lang is $LANG
- 若要将变量再其他子程序执行。需要设置export PATH变成环境变量
- 取消变量使用unset。 比如： unset myname
- set可以观察所有变量

### 常见的环境变量

- HOME:代表用户的根目录
- SHELL: 表示使用的shell版本
- PATH:执行文件查找的路径
- LANG：语系数据

### 提示字符的设置（PS1）

输出变量中有一些系统自带的符号意义：

- \d ：可显示出“星期 月 日”的日期格式，如："Mon Feb 2"
- \H ：完整的主机名称。举例来说，鸟哥的练习机为“study.centos.vbird”
- \h ：仅取主机名称在第一个小数点之前的名字，如鸟哥主机则为“study”后面省略
- \t ：显示时间，为 24 小时格式的“HH:MM:SS”
- \T ：显示时间，为 12 小时格式的“HH:MM:SS”
- \A ：显示时间，为 24 小时格式的“HH:MM”
- \@ ：显示时间，为 12 小时格式的“am/pm”样式
- \u ：目前使用者的帐号名称，如“dmtsai”；
- \v ：BASH 的版本信息，如鸟哥的测试主机版本为 4.2.46（1）-release，仅取“4.2”显示
- \w ：完整的工作目录名称，由根目录写起的目录名称。但主文件夹会以 ~ 取代；
- \W ：利用 basename 函数取得工作目录名称，所以仅会列出最后一个目录名。
- # ：下达的第几个指令。
- $ ：提示字符，如果是 root 时，提示字符为 # ，否则就是 $ 
- $$:表示当前shell的进程号(PID)
- $?: 表示上个命令的返回值

### 语系变量

    locale -a
输出当前系统支持的语系

## 读取变量

```
read [-pt] variable

-p: 后面接提示信息
-t: 后面接等待的时间
```

## 环境变量的设置

首先环境变量和如下文件有关：

- /etc/profile: 第一启动文件，系统环境变量，所有用户登录都会执行这个文件， 添加 export PATH=/home/yan/anaconda2/bin:$PATH
- /etc/environment：第二启动文件，系统环境变量，所有用户登录都会执行这个文件 直接添加到path里即可。
- ~/.profile：只对当前用户有效 export PATH=/home/yan/anaconda2/bin:$PATH
- ~/.bashrc： 该文件会被~/.profile执行，添加 export PATH=/home/yan/anaconda2/bin:$PATH

添加语句：

```
export PATH=/home/yan/anaconda2/bin:$PATH
保存后执行
source ~/.profile
```

## 管道命令

数据需要经过几道命令处理时，可以使用管道命令(|)。
管道命令竟能处理经前一命令传来的正确信息，也就是标准输出信息，对于标准错误信息没有直接处理的能力

    ls -al /etc | less

### cut和grep

cut可以将一段信息的某一段给它切出来，处理的信息是以行为单位

```
cut -d'分割符' -f fields
```

grep则是分析一行的信息，若有需要的信息则取出

```
grep [-acinv] [--color=auto] '查找字符' filename

last | grep 'root' #将last中有root的一行取出。
```

# bash脚本

## 基础语法

案例演示：

```
#!/bin/bash #声明此文件用bash这个shell来执行
# Program:
#   This program shows "hello world"
echo -e "hellor world! \a \n"
exit 0 #程序返回代码
```

一个良好的脚本需含有程序内容和时间的注释

### 交互式脚本

```
#!/bin/bash
read -p "Please input your first name: " firstname
read -p "Please input your last name: " lastname
echo -e "your full name is: ${firstname} ${lastname}\n"
```

### 数值运算

采用 var=((运算内容))来进行运算

```
  1 #!/bin/bash
  2 echo -e "you should input 2 numbers, I will multiplying them!\n"
  3 read -p "first number: " first
  4 read -p "second number: " secnu
  5 total=$((${first}*${secnu}))
  6 echo -e "first * second = ${total}"
  7 exit 0
```

### source和bash执行的差异

两者的差异：

- bash: 会新建一个bash子进程来执行，执行完毕后，子进程的变量就会释放掉。
- source: 在父进程执行，不会释放。


## 判断式和判断符号

即使用test来判断

    test -e /dmtsai #判断文件是否存在

test还可以测试文件类型、权限、字符串等数据。

使用判断符号([])判断语法如下：

    [ "$HOME" == "$SMAL" ]

注意：

- 中括号内的每个组件都要用空格隔开
- 中括号的变量也要以双引号隔开
- 中括号的常数也要用引号隔开。

## shell脚本的默认变量

为了使用命令行传参，可以使用默认变量来设置，如下：

```
/path/to/scriptname opt1 opt2 opt3 opt4
        $0           $1   $2   $3   $4
        
echo "the name is ${0}"
echo "the paramerter number is $#"
```

这些默认变量包括：

- $n: 依次代表脚本文件名、参数1、参数2
- $#: 参数的个数
- $@: 代表["$1" $2" $3" $4"]

## 函数

```
function name() #内置变量和命令行传参一样 使用${0}.....
{
    .....
}
```


## 语法 判断 循环

### 条件判断式

```
if [ condition1 ]; then
    ....
elif [ condition2 ]; then
    ...
else
    ...
fi

```

### case语句

```
case ${1} in
    "hello")
        .....
    ;;
    "")
        ...
    ;;
    *)
        ...
    ;;
esac

```

### 循环 while和for

```bash
while [ condition ]
do
    ....
done


for var in con1 con2 con3
do
    ...
done

for ((i=1; i <= 10; i++))
do
    ...
done
```

# 安装ohmyzsh

```
第一步 → 把 oh-my-zsh 项目 Clone 下来：
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

第二步 → 复制 .zshrc
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

第三步 → 更改你的默认 Shell
chsh -s /bin/zsh

~/.zshrc，ZSH_THEME="ys"
```

> 注意：ohmyzsh和.zshrc需要放到root或者当前用户目录下

# 编译指令

- ldd 该命令用于打印程序或者库文件所依赖的共享库列表
- objdump -D a.out