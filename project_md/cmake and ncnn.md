# cmake and ncnn

标签（空格分隔）： project

---

# cmake

管理c/c++各种库和各种版本的管理工具

## 使用

- 进入要执行项目的文件夹: cd projectname
- 创建build: mkdir build
- 进入build: cd build
- 建立cmake工程缓存:cmake ..
- 进行debug模式编译: cmake --build .   或加入-g 参数；-j4 使用 4个线程编译
- cmake --build . --target install
- 进行release模式编译: cmake --build . -- /p:Configuration=Release 或者 --config Release
```
cmake --build . --config Release --target install -j6
```
- cmake install 生成lib和include文件
- cmake -G "Visual Studio 16 2019" 选择vs19编译

## 使用案例

```
mkdir build
cd ./build
cmake  -G "Visual Studio 16 2019" ..
cmake --build . -j6
cmake --build . --target install -j6
```

```
cmake --taget命令详解

--target选项,默认了all

add_executable(hello)
add_executable(good)
将对于生成两个可执行文件

--target hello就只生成hello
```



### cmake语法案列

```
cmake_minimum_required(VERSION 3.5)
project(hello_library)

#根据Hello.cpp生成动态库
add_library(hello_library SHARED 
    src/Hello.cpp
)
#给动态库hello_library起一个别的名字hello::library
add_library(hello::library ALIAS hello_library)

#为这个库目标，添加头文件路径，PUBLIC表示包含了这个库的目标也会包含这个路径
target_include_directories(hello_library
    PUBLIC 
        ${PROJECT_SOURCE_DIR}/include
)


#根据main.cpp生成可执行文件 用所有的源文件生成一个可执行文件
add_executable(hello_binary
    src/main.cpp
)

#链接库和可执行文件，使用的是这个库的别名。
target_link_libraries( hello_binary
    PRIVATE 
        hello::library
)
```


## cmake语法

### 基础语法

```
cmake_minimum_required(VERSION 3.5) #设置CMake最小版本

project (hello_cmake) #设置工程名 会自动生成PROJECT_NAME变量 ${PROJECT_NAME}是变量值

add_executable(hello_cmake main.cpp) #生成可执行文件 第一个参数是可执行文件名，第二个参数是要编译的源文件列表。
add_executable(${PROJECT_NAME} main.cpp)

```

### 环境变量名

| Variable                 | Info                                                       |
| ------------------------ | ---------------------------------------------------------- |
| CMAKE_SOURCE_DIR         | 根源代码目录，工程顶层目录。暂认为就是PROJECT_SOURCE_DIR   |
| CMAKE_CURRENT_SOURCE_DIR | 当前处理的 CMakeLists.txt 所在的路径                       |
| PROJECT_SOURCE_DIR       | 工程顶层目录                                               |
| CMAKE_BINARY_DIR         | 运行cmake的目录。外部构建时就是build目录                   |
| CMAKE_CURRENT_BINARY_DIR | The build directory you are currently in.当前所在build目录 |
| PROJECT_BINARY_DIR       | 暂认为就是CMAKE_BINARY_DIR                                 |

### 创建变量名

```
set(SOURCES
    src/Hello.cpp
    src/main.cpp
)#创建一个变量，名字叫SOURCE。它包含了所有的cpp文件。

add_executable(hello_headers ${SOURCES})#用所有的源文件生成一个可执行文件
```

### 设置需要包含的库

target_include_directories（）添加了一个目录，这个目录是库所包含的头文件的目录，并设置库属性为PUBLIC。
这个目录会在如下被调用：

- 编译这个库的时候，Hello.cpp的函数定义在Hello.h中，所以编译会用到这个头文件
- 编译链接这个库hello_library的任何其他目标

```
target_include_directories(hello_headers
    PRIVATE 
        ${PROJECT_SOURCE_DIR}/include
)#设置这个可执行文件hello_headers需要包含的库的路径

#PROJECT_SOURCE_DIR指工程顶层目录
#PROJECT_Binary_DIR指编译目录 build目录
可以使用message()命令输出这些变量
```

### 包含静态库和动态库

add_library(hello_library STATIC/SHARED 代表静态或动态 
    src/Hello.cpp
) 

```
文件树
├── CMakeLists.txt
├── include
│   └── static
│       └── Hello.h
└── src
    ├── Hello.cpp
    └── main.cpp


add_library(hello_library STATIC 
    src/Hello.cpp
) #库的源文件Hello.cpp生成静态库hello_library

target_include_directories(hello_library
    PUBLIC 
        ${PROJECT_SOURCE_DIR}/include
)#为一个目标添加头文件路径

#指定用哪个源文件生成可执行文件
add_executable(hello_binary 
    src/main.cpp
)
#链接可执行文件和静态库
target_link_libraries( hello_binary
    PRIVATE 
        hello_library
)
```

### 链接库

add_executable()连接源文件，target_link_libraries()连接库文件

### private pubic interface的范围详解

target_include_directories会设置头文件的包含：

- PRIVATE - 目录被添加到目标（库）的包含路径中。
- INTERFACE - 目录没有被添加到目标（库）的包含路径中，而是链接了这个库的其他目标（库或者可执行程序）包含路径中
- PUBLIC - 目录既被添加到目标（库）的包含路径中，同时添加到了链接了这个库的其他目标（库或者可执行程序）的包含路径中



## install的使用

install的功能：将项目生成的库文件、头文件、可执行文件或相关文件等安装到指定位置。

这主要是通过`install`方法在CMakeLists.txt中配置，`make install`命令安装相关文件来实现的。

```cmake
cmake_minimum_required(VERSION 3.0)
project(Installation VERSION 1.0)

# 如果想生成静态库，使用下面的语句
# add_library(mymath mymath.cc)
# target_include_directories(mymath PUBLIC ${CMAKE_SOURCE_DIR}/include)

# 如果想生成动态库，使用下面的语句
add_library(mymath SHARED mymath.cc)
target_include_directories(mymath PRIVATE  ${CMAKE_SOURCE_DIR}/include)
set_target_properties(mymath PROPERTIES PUBLIC_HEADER ${CMAKE_SOURCE_DIR}/include/mymath.h)

# 生成可执行文件
add_executable(mymathapp mymathApp.cc)
target_link_libraries(mymathapp mymath)
target_include_directories(mymathapp PRIVATE ${CMAKE_SOURCE_DIR}/include)

install(TARGETS MyLib
        EXPORT MyLibTargets 
        LIBRARY DESTINATION lib  # lib表示动态库安装路径
        ARCHIVE DESTINATION lib  # lib表示静态库安装路径
        RUNTIME DESTINATION bin  # bin表示可执行文件安装路径
        PUBLIC_HEADER DESTINATION include  # 头文件安装路径
        )
 
# 命令行运行
mkdir build
cd ./build
cmake ..
cmake --build . --targ
       
```



### 包含第三方库

案例：

```
cmake_minimum_required(VERSION 3.5)

# Set the project name
project (third_party_include)
# find a boost install with the libraries filesystem and system
#使用库文件系统和系统查找boost install
find_package(Boost 1.46.1 REQUIRED COMPONENTS filesystem system)
#这是第三方库，而不是自己生成的静态动态库
# check if boost was found
if(Boost_FOUND)
    message ("boost found")
else()
    message (FATAL_ERROR "Cannot find Boost")
endif()

# Add an executable
add_executable(third_party_include main.cpp)

# link against the boost libraries
target_link_libraries( third_party_include
    PRIVATE
        Boost::filesystem
)
```


查找库：find_package(Boost 1.46.1 REQUIRED COMPONENTS filesystem system)

- Boost-库名称。 这是用于查找模块文件FindBoost.cmake的一部分
- 1.46.1 - 需要的boost库最低版本
- REQUIRED - 告诉模块这是必需的，如果找不到会报错
- COMPONENTS - 要查找的库列表。从后面的参数代表的库里找boost
- 会产生Boost_FOUND变量，于检查软件包在系统上是否可用。


# ncnn 

ncnn生成的两个文件：param是网络结构文件，bin是网络权重文件。

## 编译ncnn

```
-G "Visual Studio 16 2019"
-G"NMake Makefiles"

cmake -G"NMake Makefiles" -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=Release/install -DProtobuf_INCLUDE_DIR=C:/protobuf-3.4.0/build-2019/installRelease/include -DProtobuf_LIBRARIES=C:/protobuf-3.4.0/build-2019/installRelease/libprotobuf.lib -DProtobuf_PROTOC_EXECUTABLE=C:/protobuf-3.4.0/build-2019/installRelease/bin/protoc.exe -DNCNN_VULKAN=ON ..

-DOpenCV_DIR=C:/c++/opencv/opencv/build -DNCNN_VULKAN=ON .. 

nmake #vs2019自带的工具

nmake 
nmake install
```

## ncnn param参数介绍

```
model.param如下：
7767517
24 24
Input       data    0 1 data 0=227 1=227 2=3
Convolution conv1   1 1 data conv1 0=96 1=11 2=1 3=4 4=0 5=1 6=34848
```

>前置信息
- 7767517代表版本信息
- 24 24 代表层和数据结构（blob）数量
- blob 即一个数组（N，C, H, W）依次表示batch_size、chanle、height、weight
- - blob 即一个数组（C，N, H, W）依次表示batch_size、chanle、height、weight

>layer: Input  data  0  1     data  0=227  1=227  2=3 
- 层的类型：Input
- 层的名称：data
- 输入数据结构（blob）数量bottom：0
- 输出数据结构（blob）数量top：1
- 网络输入层数据结构(blob)：没有
- 网络输出数据结构(blob)：data
- 特殊参数层：没有
- 0=227  1=227  2=3  依次表示w、h、c
- 卷积的参数： 0=96 1=11 2=1 3=4 4=0 5=1 6=34848 卷积核的数量 kernel_size dilation stride pading bias 特殊参数

## 踩坑记录

- 编译器一致，统一使用msvc编译器，不同编译器产生的链接库也是不一样的
- cmake查找库时也会从环境变量中查找 尽量不要配置环境变量
- debug和release的版本一定要统一对应
- 编译会缺少glslang和python需要自己git


## onnx到ncnn

### 转换onnx

```
#注意model要加modedule 
torch_out = torch.onnx.export(newmodel.module, x, "img2pose.onnx", training=False, verbose=False, opset_version=11)

#onnx模型简化
python3 -m onnxsim input_model output_onnx_model

#生成ncnn模型
onnx2ncnn model.onnx model.param model.bin 

#netron查看网络结构
import netron
modelPath = "你的模型文件名.扩展名"
netron.start(modelPath)

#利用ncnnoptimize工具 将无用的memorydata删除 并自动设置合适的数量
./ncnnoptimize model.param model.bin model-opt.param model-opt.bin 0
```

## pnnx到ncnn

编译的版本必须一致


```
import torch
import torchvision.models as models

net = models.resnet18(pretrained=True)
net = net.eval()

x = torch.rand(1, 3, 224, 224)
mod = torch.jit.trace(net, x)
torch.jit.save(mod, "resnet18.pt")


#pnnx resnet18.pt inputshape=[1,3,224,224]
```

# linux编译ncnn工程

## 操作


```
scl enable devtoolset-8 bash #切换到gcc 8
注释DBG_CODE
替换make_unique宏
std::wcout和printf输出不保持一致
```