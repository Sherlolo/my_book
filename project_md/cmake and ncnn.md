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
-DCMAKE_CXX_STANDARD=11 #c++11
```
- cmake install 生成lib和include文件
- cmake -G "Visual Studio 16 2019" 选择vs19编译
- -G"MinGW Makefiles"  使用mingw编译

## 使用案例

```c++
mkdir build
cd ./build
cmake  -G "Visual Studio 16 2019" ..
-G "MinGW Makefiles" //使用g
cmake --build . -j6
cmake --build . --target install -j6
//install用于指定在安装时运行的规则。它可以用来安装很多内容，可以包括目标二进制、动态库、静态库以及文件、目录、脚本等：
//在cmake里编写install 编译时会install变量名对应的lib编译成一个库
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
python3 -m onnxsim input_model output_onnx_model --input-shape 1,3,48,320

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

# ncnn源码阅读

## 异常设计

```c++
int fun();  //异常状态码通过返回值传递
```



## 内存构造

ncnn里的内存可根据需要，实现地址16子节对齐。

> 内存对齐的原因：
>
> 平台原因：是所有的硬件平台都能访问任意地址上的任意数据的；某些硬件平台只能在某些地址处取某些特定类型的数据，否则抛出硬件异常。
>
> 性能原因：数据结构(尤其是栈)应该尽可能地在自然边界上对齐。原因在于，为了访问未对齐的内存，处理器需要作两次内存访问；而对齐的内存访问仅需要一次访问。



```c++
#include <bits/stdc++.h>

#define ll long long
void debug() {
#ifdef Acui
  freopen("data.in", "r", stdin);
  freopen("data.out", "w", stdout);
#endif
}

using namespace std;

// the alignment of all the allocated buffers
#define MALLOC_ALIGN 16

//取>=sz的能被n整除的最小地址
static inline size_t alignSize(size_t sz, int n) {
  return (sz + n - 1) & -n;
}


//取>=sz的能被n整除的指针地址
template<typename _Tp>
static inline _Tp* alignPtr(_Tp* ptr, int n = (int)sizeof(_Tp)) {
  return (_Tp*)(((size_t)ptr + n - 1) & -n);
}


static inline void* fastMalloc(size_t size) {
  unsigned char* udata = (unsigned char*)malloc(size + sizeof(void*) + MALLOC_ALIGN); //为了对齐需多分配一些内存
  if (!udata)
    return 0;
  unsigned char** adata = alignPtr((unsigned char**)udata + 1, MALLOC_ALIGN);	//返回对齐后的子节 (unsigned char**)是为了移动8个子节
  adata[-1] = udata;	//adata的首位存放最初的地址
  return adata;
}

static inline void fastFree(void* ptr) {
  if (ptr) {
    unsigned char* udata = ((unsigned char**)ptr)[-1];
    free(udata);
  }
}

int main() {
  int *a = new int(5);
  std::cout << "a: " << a << std::endl;
  std::cout << "(size_t)a: " << (size_t)a << std::endl;
  std::cout << "(ll)a: " << (ll)a << std::endl;

  size_t totalsize = alignSize(12, 4);
  size_t totalsize2 = alignSize(17, 4);
  std::cout << "totalsize: " << totalsize << std::endl;
  std::cout << "totalsize: " << totalsize2 << std::endl;
  void* data = fastMalloc(totalsize);
  fastFree(data);
  std::cout << "data:         " << data << std::endl;
  std::cout << "(void*)data:  " << (void*)data << std::endl;
  std::cout << "(int*)data:   " << (int*)data << std::endl;
  std::cout << "(float*)data: " << (float*)data << std::endl;
  return 0;
}
```



## Mat类

内存分配除了矩阵数据外，计数值也分配了

```cpp
inline void Mat::create(int _w, int _h, int _c)
{
    // 第一部分
    release();

    // 第二部分
    dims = 3;
    w = _w;
    h = _h;
    c = _c;
    cstep = alignSize(w * h * sizeof(float), 16) >> 2;	//为了访问下一个通道时的缩进值

    // 第三部分
    if (total() > 0)
    {
        size_t totalsize = total() * sizeof(float);
        data = (float*)fastMalloc(totalsize + (int)sizeof(*refcount)); //内存分配除了矩阵数据外，计数值也分配了
        refcount = (int*)(((unsigned char*)data) + totalsize);
        *refcount = 1;
    }
}
```



## ncnn网络推理过程

这个大哥小弟的例子还是我写这个文章的时候无意想到的，觉得很有意思。ncnn推理有一个特点，那就是从底往上递归深搜最短路跑的，这个我觉得用大哥小弟来举例子就很好。这里我先写了怕后面忘了。假定有人给了大哥一笔钱，让他把钱分给小弟(用户调用了input方法塞数据了)，等会会有小弟来要钱(用户调用了extract方法要数据)。这时候大哥有两个方案：1.老老实实给每一个小弟发钱，2.哪个小弟要钱单独给他发。考虑到一个复杂的网络，有很多的大哥、中哥、小哥、大第、中弟、小弟。发钱是一级一级往下发，小弟要钱是一级一级往上要，每一个要钱都是要底下所有人的份子。我们分析一下这两个方案：

- 方案一：每个人都发，大家都有钱，有些小弟还不需要钱，你也给他发了
    - 优点：每个人你都发了，缺钱也别来问，都给你了（直接完整推理，要什么数据取就行了）
    - 缺点：大哥都发出去了，累死累活的（全部计算量）

- 方案二：来要钱才给他，有些不要钱的不给了

    - 优点：大哥省事，谁要给谁（节省计算量）
    - 缺点：每个小弟要钱都要往上打报告，大哥再给他们发（取不同节点数据中间需要再推理）

    

源码解析：

```c++
// 网络加载
ncnn::Net squeezenet;
squeezenet.load_param("squeezenet_v1.1.param");
squeezenet.load_model("squeezenet_v1.1.bin");

// 数据预处理
ncnn::Mat in = ncnn::Mat::from_pixels_resize(image.data, ncnn::Mat::PIXEL_BGR, image.cols, image.rows, 227, 227);
const float mean_vals[3] = { 104.f, 117.f, 123.f };
in.substract_mean_normalize(mean_vals, 0);

// 网络推理
ncnn::Extractor ex = squeezenet.create_extractor();
ex.input("data", in);
ncnn::Mat out;
ex.extract("prob", out);
```



1. ncnn::Extractor或者说是create_extractor，这个其实就是一个专门用来维护推理过程数据的一个类，跟ncnn::Net解耦开，不糊弄到一块而已，这个最主要的就是开辟了一个大小为网络的blob size的std::vector\<ncnn::Mat>来维护计算中间的数据
2. input，这个更简单，就是在上一步开辟的vector中，把该input的blob的数据in塞进去
3. extract，这个东西就比较核心了，我们这回主要看这个
4. 每次运算到blob前的结果都会保存下来。

整体思路：

1. 读取param和bin文件，记录下每一层的layer、layer的输入输出节点、layer的特定参数

2. 推理

3. 1. 维护一个列表用于存所有节点的数据

    2. 给输入节点放入输入数据

    3. 计算输出节点的layer

    4. 1. 计算layer所需的输入节点还没给输入——>递归调用上一层layer计算
        2. 有输入了——>计算当前layer
        3. 输出结果

## 卷积计算

卷积计算的小技巧，sun初值赋值成bias，避免二次遍历

使用openmp加速

```cpp
#pragma omp parallel for num_threads(opt.num_threads)
for (int p=0; p<num_output; p++)
    {
        float* outptr = top_blob.channel(p);

        for (int i = 0; i < outh; i++)
        {
            for (int j = 0; j < outw; j++)
            {
                float sum = 0.f;

                if (bias_term)
                    sum = bias_data.data[p]; // 加bias

                const float* kptr = weight_data_ptr + maxk * channels * p;

                // channels
                for (int q=0; q<channels; q++)
                {
                    const Mat m = bottom_blob_bordered.channel(q);
                    const float* sptr = m.data + m.w * i*stride + j*stride;

                    for (int k = 0; k < maxk; k++)
                    {
                        float val = sptr[ space_ofs[k] ];
                        float w = kptr[k];
                        sum += val * w; // kernel和feature的乘加操作
                    }

                    kptr += maxk;
                }

                outptr[j] = sum;
            }

            outptr += outw;
        }
    }
```

## 运算加速

- fill使用avx2

```c++
NCNN_FORCEINLINE void Mat::fill(__m256 _v)
{
    int size = (int)total();
    float* ptr = (float*)data;
    for (int i = 0; i < size; i++)
    {
        _mm256_storeu_ps(ptr, _v);
        ptr += 8;
    }
}

```

- extractor的设计模型

使用指针存放数据

```c++
class NCNN_EXPORT Extractor
{
private:
    ExtractorPrivate* const d; //存放每次运行的数据
}
```

