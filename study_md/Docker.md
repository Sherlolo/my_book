# Docker

标签（空格分隔）： study

---

# Docker 概述

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

开发者使用docker打包环境和工程完成项目的部署。
不再需要运维来部署环境

## 虚拟化技术和容器化技术

容器化技术也是一种虚拟化技术

虚拟化技术特点：

- 资源占用多 
- 冗余步骤多 
- 启动很慢

docker和虚拟机的不同：

- 传统虚拟机，虚拟出硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件。
- Docker容器内的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟硬件。
- 每个容器都是相互隔离的，每个容器都有属于自己的文件系统，互不影响。

## Docker的基本组成

![image.png-240kB](http://static.zybuluo.com/ShowArcher/qy4fbsm6dxkmdq7f7kg5voh3/image.png)

专业名词：

- 镜像(image): docker镜像类似一个模板，可以通过这个镜像创建多个容器
- 容器(container)：docker利用容器化技术，独立运行或一个组应用，通过镜像来创建
- 仓库(repository): 仓库就是存放镜像的地方。类似于github


# docker的安装

```
#卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

#下载安装包
yum install -y yum-utils

#设置镜像的仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  #国外的地址
    
    # 设置阿里云的Docker镜像仓库
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  #国外的地址

#更新yum软件包索引
yum makecache fast

#安装docker相关的配置
yum install docker-ce docker-ce-cli containerd.io

```

## 启动docker

```
#启动docker
systemctl start docker
# 查看当前版本号，是否启动成功
docker version

# 设置开机自启动
systemctl enable docker
```

## docker的卸载


```
#docker的卸载
# 1. 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2. 删除资源  . /var/lib/docker是docker的默认工作路径
rm -rf /var/lib/docker
```

# docker的运行

![image.png-105.1kB](http://static.zybuluo.com/ShowArcher/oyo9vaaql1zkyn3mfly1bvks/image.png)

docker运行比较快的原因：

- Docker比虚拟机更少的抽象层
- docker利用宿主机的内核，VM需要的是Guest OS


# docker的常用命令

## 基础命令

```
docker version          #查看docker的版本信息
docker info             #查看docker的系统信息,包括镜像和容器的数量
docker 命令 --help       #帮助命令(可查看可选的参数)
docker COMMAND --help
```

## 镜像命令

### 查看镜像

docker images  参数如下：

- -a/--all 列出所有镜像
- -q/--quiet 只显示镜像的id


```
#查看镜像 docker images
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   11 months ago   13.3kB

#解释:
1.REPOSITORY  镜像的仓库源
2.TAG  镜像的标签
3.IMAGE ID 镜像的id
4.CREATED 镜像的创建时间
5.SIZE 镜像的大小

```


### 搜索和下载镜像

    docker search 镜像
将会从远程仓库里查找镜像

    docker pull 镜像名[:tag]
下载镜像

```
docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10308     [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3819      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   754                  [OK]
percona                           Percona Server is a fork of the MySQL relati…   517       [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   86
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   79
centurylink/mysql                 Image containing mysql. Optimized to be link…   60                   [OK]

      
      
#搜索收藏数大于3000的镜像
docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10308     [OK]
mariadb   MariaDB is a community-developed fordockerk of MyS…   3819      [OK]


#下载镜像 
docker pull mysql

Using default tag: latest            #如果不写tag默认就是latest
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete          #分层下载,docker image的核心-联合文件系统
fedd960d3481: Pull complete
7ab947313861: Pull complete
64f92f19e638: Pull complete
3e80b17bff96: Pull complete
014e976799f9: Pull complete
59ae84fee1b3: Pull complete
ffe10de703ea: Pull complete
657af6d90c83: Pull complete
98bfb480322c: Pull complete
6aa3859c4789: Pull complete
1ed875d851ef: Pull complete
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #下载来源的真实地址  #docker pull mysql等价于docker pull docker.io/library/mysql:latest


#下载指定版本
docker pull mysql:5.7 
```

### 删除镜像

```
#删除镜像 docker rmi
#1.删除指定的镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  镜像id
#2.删除多个镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  镜像id 镜像id 镜像id
#3.删除全部的镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  $(docker images -aq)

```

## 容器命令

### 运行容器

docker run [可选参数] image #运行

- -d: 后台运行容器，并返回容器ID；
- -i: 以交互模式运行容器，通常与 -t 同时使用；
- -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
- -p: 指定端口映射，格式为：主机(宿主)端口:容器端口

```
#运行容器 
docker run -it centos /bin/bash #运行并进入容器centos
 
#推出
exit #退出容器
Ctrl+P+Q  #不停止容器退出

#列出容器
docker ps  # 列出当前正在运行的容器
-a   # 列出所有容器的运行记录
-n=? # 显示最近创建的n个容器
-q   # 只显示容器的编号
```

### 删除容器 停止容器

```
# 删除容器
docker rm 容器id                 #删除指定的容器,不能删除正在运行的容器,强制删除使用 rm -f
docker rm -f $(docker ps -aq)   #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器

#启动和停止容器
docker start 容器id          #启动容器
docker restart 容器id        #重启容器
docker stop 容器id           #停止当前运行的容器
docker kill 容器id           #强制停止当前容器

- docker exec: 进入容器后开启一个新的终端，可以在里面操作。
- docker attach： 进入容器正在执行的终端，不会启动新的进程。

 docker exec -it c703b5b1911f /bin/bash

#退出
exit #退出容器
Ctrl+P+Q  #不停止容器退出
```


## 其他常用命令

### 日志的查看

dokcer logs 命令

- -f: follow log output
- -t: timestamps show timestamps


```
docker logs -tf 容器id
docker logs -tf --tail number 容器id #num为要显示的日志条数


docker run -d centos /bin/sh -c "while true;do echo hi;sleep 5;done" #编写脚本循环执行
```

### 查看容器中的进程信息

docker top 容器id

```
docker top c703b5b1911f

UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                11156               11135               0                   11:31               ?                   00:00:00            /bin/sh -c while true;do echo hi;sleep 5;done
root                11886               11156               0                   11:43               ?   

```

### 查看容器的元数据

    docker inspect 容器id 

可以查看容器的详细信息，id的全称，网络信息

### 进入容器内部

前提容器正在运行，主要有两种方式：

- docker exec: 进入容器后开启一个新的终端，可以在里面操作。
- docker attach： 进入容器正在执行的终端，不会启动新的进程。

```
 docker exec -it c703b5b1911f /bin/bash
```

### 拷贝操作

容器内部文件和主机文件的相互传输拷贝

```
#拷贝容器的文件到主机中
docker cp 容器id:容器内路径  目的主机路径

#拷贝宿主机的文件到容器中
docker cp 目的主机路径 容器id:容器内路径

```

## 常用命令总结

![image.png-293.1kB](http://static.zybuluo.com/ShowArcher/8z6xcayem4ifdr793341ldnj/image.png)

# Docker镜像详解

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需要的所有内容，包括代码、库，环境变量和配置文件。

## Docker镜像加载原理

Docker的文件系统时unionFS联合文件系统，Docker的镜像实际上是一层一层的文件系统组成，类似于git的文件系统。

Docker的镜像都是只读的。当容器启动时，一个新的可写层被加载到镜像的顶部。
而这一层就是我们通常说的容器层，容器层之下的都叫镜像层。

![image.png-61.7kB](http://static.zybuluo.com/ShowArcher/r1a24atic0rmvst5ngtjhsh7/image.png)

## 提交镜像

使用docker commit提交一个新的镜像

```
docker commit -m=“提交的描述信息”  -a="作者" 容器id 目标镜像名:[TAG] 

docker commit -m="add webapps" -a="sherlolo" 2a3bf3eaa2e4 mytomcat:1.0
```

# Docker容器数据卷详解

## 数据卷介绍

数据卷就是目录或文件，存在于一个或多个容器中，由Docker挂载到容器，但卷不属于联合文件系统（Union FileSystem），因此能够绕过联合文件系统提供一些用于持续存储或共享数据的特性。

数据卷的设计：数据卷的设计采用的是备份的思想，而不是共享的思想，即每个容器和宿主机的文件相互拷贝来实现共享和持续存储
卷的设计目标：数据的持久化和数据共享。完全独立于容器的生存周期，因此Docker不会在容器删除时而删除挂载的数据卷。

## 简单使用

数据卷挂载中，如果没有指定目录，将自动在"/var/lib/docker/volumes"下生成对应的文件路径
每次新运行一个容器，将会创建或覆盖掉数据卷

```
#数据卷挂载
docker run -it -v 主机目录:容器目录 #指定路径挂在
docker run -it -v 容器目录 #匿名挂载
docker run -it -v 名字:容器目录 #具名挂载
#设置数据卷的读写属性 匿名卷默认会新建一个新的宿主机数据卷 或者覆盖
docker run -d -P --name nginx02 -v Juming_nginx:/etc/nginx:ro nginx  #ro代表对于容器只读， 宿主机可以进行操作
docker run -d -P --name nginx02 -v Juming_nginx:/etc/nginx:rw nginx

#查看数据卷
docker volume ls #查看数据卷
docker volume inspect 数据卷名
```

# Dockerfile的编写

## Dockerfile介绍

dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。

构建步骤：

1、编写DockerFile文件
2、docker build 构建镜像
3、docker run

### Dockerfile、Docker、container三者的联系

三者的联系如下：

- Dockerfile：软件的原材料（代码），面向开发者
- Docker镜像：软件的交付品(.cpp)
- Docker容器：软件的运行状态(.exe)

## Dockerfile指令

关键字
```
FROM        # 基础镜像，当前新镜像是基于哪个镜像的 
MAINTAINER  # 镜像维护者的姓名混合邮箱地址 
RUN         # 容器构建时需要运行的命令 
EXPOSE      # 当前容器对外保留出的端口 
WORKDIR     # 指定在创建容器后，终端默认登录的进来工作目录，一个落脚点 
ENV         # 用来在构建镜像过程中设置环境变量 
ADD         # 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包 
COPY        # 类似ADD，拷贝文件和目录到镜像中！ 
VOLUME      # 容器数据卷，用于数据保存和持久化工作 
CMD         # 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最 后一个生效！ 
ENTRYPOINT  # 指定一个容器启动时要运行的命令！和CMD一样 
ONBUILD     # 当构建一个被继承的DockerFile时运行命令，父镜像在被子镜像继承后，父镜像的 ONBUILD被触发
```

## 实践案列

Docker Hub 中99% 的镜像都是通过在base镜像（Scratch）中安装和配置需要的软件构建出来的

### 构建自己的centos

```dockerfile
#编写dockerfile文件
FROM centos:centos7
MAINTAINER sherlolo

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----END----"
CMD /bin/bash

#构建镜像
docker build -f mydockerfile-centos -t mycentos:0.1

#运行容器
docker run -it mycentos:0.1

#列出镜像的变更历史
docker history ID

```

# 发布镜像

## 通过dockerhub发布

```
#登录账户
docker login -u 用户名 -p 密码

#镜像发布
docker push sherlolo/centos:0.1 

#修改本地镜像信息 添加用户名
docker tag  251ca4419332 sherlolo/ce`ntos:0.1


```

## 本地发布镜像

```bash
docker commit -m=“提交的描述信息”  -a="作者" 容器id 目标镜像名:[TAG] 

docker commit -m="add webapps" -a="sherlolo" 2a3bf3eaa2e4 mytomcat:1.0

#镜像保存为本地文件
docker save -o file.tar imageid(imagenaem)

#修改tag信息
docker tag  251ca4419332 sherlolo/centos:0.1

#加载镜像
docker load --input file.tar
```



# docker基础命令准备

 ![image.png-334.6kB](http://static.zybuluo.com/ShowArcher/9btcvo35qxgifyogxi68oky8/image.png)

 