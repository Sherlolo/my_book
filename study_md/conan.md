[本篇文章的代码都在这里备份](https://gitee.com/makesex/conan_demo)

@[toc]
## 一、conan介绍
1. 跨平台
2. c、c++包管理工具
3. python写的
4. 开源
5. 类似java的maven，python的pip
### 1.1 简单介绍
- Conan是C和C ++语言的依赖项和程序包管理器。它是免费和开源的，并且可以在所有平台上使用：Windows，Linux，OSX，FreeBSD，Solaris等，并且可以用于开发所有目标，包括嵌入式，移动（iOS，Android）和裸机。它还与所有构建系统集成，例如CMake，Visual Studio（MSBuild），Makefile，SCons等，包括专有系统。

- 它是专门为加速C和C ++项目的开发和持续集成而设计和优化的。借助完全的二进制管理，它可以在所有平台上使用完全相同的过程为包的任意数量的不同版本创建和重用任意数量的不同二进制文件（用于不同的配置，如体系结构，编译器版本等）。由于它是分散式的，因此很容易运行您自己的服务器以私下托管您自己的软件包和二进制文件，而无需共享它们。建议使用免费的JFrog Artifactory社区版（CE），由Conan服务器在您的控制下私下托管您自己的程序包。

---
### 1.2 conna特点

- Conan成熟稳定，对向前兼容性（不间断政策）有坚定的承诺，并有一个完整的团队全职致力于其改进和支持。它由一个伟大的社区支持和使用，从ConanCenter中的开源贡献者和包创建者到数千个使用它的团队和公司。

- Conan是具有客户端-服务器体系结构的分散式软件包管理器。这意味着客户端可以从不同的服务器获取软件包，也可以将软件包上载到不同的服务器（“远程”），类似于“ git”推拉模型到/从git远程服务器。

- 从较高的角度来看，服务器只是程序包存储。他们不构建也不创建包。这些包是由客户端创建的，并且如果二进制文件是从源代码构建的，则该编译也将由客户端应用程序完成。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404144252367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)
上图中的不同应用程序是：

- Conan客户端：这是一个控制台/终端命令行应用程序，其中包含用于程序包创建和使用的繁琐逻辑。Conan客户端具有用于程序包存储的本地缓存，因此它使您可以完全创建和脱机测试程序包。您也可以脱机工作，只要不需要远程服务器上的新软件包即可。
- 推荐使用JFrog Artifactory Community Edition（CE），由Conan服务器在您的控制下私下托管您自己的程序包。它是JFrog Artifactory for - Conan软件包的免费社区版本，包括WebUI，多个身份验证协议（LDAP），用于创建高级拓扑的虚拟和远程存储库，Rest API和用于存储任何工件的通用存储库。
- conan_server是与Conan客户端一起分发的小型服务器。这是一个简单的开源实现，它提供基本功能，但不提供WebUI或其他高级功能。
ConanCenter是一个中央公共存储库，社区在其中为流行的开源库（例如Boost，Zlib，OpenSSL，Poco等）提供软件包。
---
### 1.3 跨平台

Conan可在Windows，Linux（Ubuntu，Debian，RedHat，ArchLinux，Raspbian），OSX，FreeBSD和SunOS上运行，并且由于具有可移植性，因此它可在可运行Python的任何其他平台上运行。它可以针对任何现有平台，从裸机到桌面，移动，嵌入式，服务器，跨架构。

Conan也可以与任何构建系统一起使用。与最流行的集成有内置的集成，例如CMake，Visual Studio（MSBuild），自动工具和Makefile，SCons等。但不需要使用任何集成。甚至没有必要所有软件包都使用相同的构建系统，每个软件包都可以使用自己的构建系统，并依赖于使用不同构建系统的其他软件包。还可以与任何构建系统（包括专有系统）集成。

同样，柯南可以管理任何编译器和任何版本。有一些最流行的默认定义：gcc，cl.exe，clang，apple-clang，intel，具有不同的版本配置，运行时，C ++标准库等。该模型还可以扩展到任何自定义配置。

## 二、conan全平台安装
无论是在什么平台，因为conan是python开发的。都可以使用python的包管理工具pip下载
```python
pip install conan==1.43.0 #1.60.0
```

安装后：
- 命令
```shell
conan --version
```
- 结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404144920858.png)

就可以看到安装的版本。

**更新conan：**

```shell
 pip install conan --upgrade  
```

## 三、使用conan教程
[本篇文章的代码都在这里备份](https://gitee.com/makesex/conan_demo)


让我们从一个示例开始：我们将创建一个MD5哈希计算器应用程序，该应用程序使用最流行的C ++库之一：Poco。

在这种情况下，我们将使用CMake作为构建系统，但请记住，柯南可与任何构建系统一起使用，而不仅限于使用CMake。

- 代码段
```c++
 #include "Poco/MD5Engine.h"
 #include "Poco/DigestStream.h"

 #include <iostream>

 int main(int argc, char** argv){
     Poco::MD5Engine md5;
     Poco::DigestOutputStream ds(md5);
     ds << "abcdefghijklmnopqrstuvwxyz";
     ds.close();
     std::cout << Poco::DigestEngine::digestToHex(md5.digest()) << std::endl;
     return 0;
 }
```

- 依赖项
我们知道我们的应用程序依赖于Poco库。让我们在ConanCenter遥控器中查找它，转到https://conan.io/center，然后在搜索框中键入“ poco”。我们将看到有一些不同的版本可用。
**或者我们直接使用命令行：**
```shell
 conan search poco --remote=conancenter
```



我们可以看到搜索结果截图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404145637544.png)

我们可以看到有很多版本的poco库版本

我对版本1.9.4比较感兴趣，所以我准备查看一下关于这个版本的信息。我输入以下指令，得到反馈。


- 命令
	```shell
	conan inspect poco/1.9.4 -remote=conancenter
	```
- 结果截图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404145844630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)
看了描述后我决定就用它了，然后我在我的项目里面建立一个名为：`conanfile.txt`的文件。
在里面写上了
- conanfile.txt
	```shell
	 [requires]
	 poco/1.9.4
	
	 [generators]
	 cmake
	
	```

接下来：我们将安装所需的依赖项并生成构建系统的信息

首先我们创建一个文件夹，里面放上我们刚刚写的依赖于库文件的.cpp文件，命名为;`md5.cpp `,然后在创建一个名为build的文件夹，一会用来构建工程。现在我们的目录结构是这样的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404151144237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)

**conan profie**

一般在/root/.conan/profiles/detects路径下

```bash
conan profile new default --detect #创建新的profile
```

在正式开始下载库文件并编译的前，我们先告诉conan使用c++11的标准来编译我们需要的库文件。
执行下面指令：

```shell
conan profile update settings.compiler.libcxx=libstdc++11 default
```

现在开始使用conan编译我们的库文件，首先进入build文件夹然后执行指令：

```shell
conan install ..  --build missing #--build=poco/1.9.4 指定库重写编译
```
如果你是初次安装就会看到下面这张截图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404150631790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)

最后安装结束。

Conan安装了我们的Poco依赖关系，还安装了传递依赖关系：OpenSSL，zlib，sqlite等。它还为我们的构建系统生成了conanbuildinfo.cmake文件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404151446847.png)
现在库文件已经安装好了，我们来写一个cmake执行我们的.cpp文件，脚本如下：

－　CMakeLists.txt

```cmake

 cmake_minimum_required(VERSION 2.8.12)
 project(MD5Encrypter)

 add_definitions("-std=c++11")

 include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
 conan_basic_setup() #这一步必须再add_executable前面

 add_executable(md5 md5.cpp)
 target_link_libraries(md5 ${CONAN_LIBS})
```
现在我们的目录结构是：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404151853207.png)

> (CMakeLists.txt  图中文件名写错了！！！！)


现在我们可以进入build文件夹开始执行了

- windows的执行命令
```
(win)
$ cmake .. -G "Visual Studio 16"
$ cmake --build . --config Release
```
- linux的执行命令
```
(linux, mac)
$ cmake .. -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
$ cmake --build .
...
[100%] Built target md5
$ ./bin/md5
c3fcd3d76192e4007dfb496cca67e13b
```
这里我是用的linux，可以看到我的执行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404152042822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404152210188.png)


我们看一眼最终的生成文件夹：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040415232382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NleHlsdW5h,size_16,color_FFFFFF,t_70)

我们需要的东西就在bin文件夹里面，进去执行一下：

```shell

root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build# cd bin
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ls
md5
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b
root@iZ2ze99clzuka1844h9qy0Z:~/conanTest/build/bin# ./md5
c3fcd3d76192e4007dfb496cca67e13b

```

可以看到已经执行成功啦。


## 四、快速总结conan
1. conna是个 c++/c 的包管理工具，基于python开发，开源。
2. conan需要编写conanfile.txt来说明依赖。
3. `conan search -r=conan `指令可以在远程仓库搜索包
4. conan install 指令来根据conanfile.txt安装库文件
5. 最终生成文件：conanbuildinfo.txt
6. 编写cmake后编译工程
7. 完成使用

- 文中所用命令

```shell
# 安装
pip install conan

# 版本
conan --version

# 升级
pip install conan --upgrade  
 
# 搜索包
conan search poco --remote=conan-center
conan search libpng -r=conan-center
  
# 查看  
conan inspect poco/1.9.4
  
# 配置文件名  
conanfile.txt
  
# 设置默认编译版本 
conan profile update settings.compiler.libcxx=libstdc++11 default

# 安装库
conan install .. 


# cmake使用例子
cmake_minimum_required(VERSION 2.8.12)
project(MD5Encrypter)

add_definitions("-std=c++11")

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

add_executable(md5 md5.cpp)
target_link_libraries(md5 ${CONAN_LIBS})
 
 
 
# linux构建命令
$ cmake .. -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
$ cmake --build .

```