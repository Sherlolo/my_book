# HPC（高性能）

# 测试工具

## windows

### 检测内存泄漏

```c++
#define _CRTDBG_MAP_ALLOC
#include <stdlib.h>
#include <crtdbg.h>

int main()
{
    _CrtDumpMemoryLeaks();
  	fun();
    _CrtDumpMemoryLeaks();  
}

//输出{62} normal block at 0x0001ad, 40bytes long
int main()
{
    _CrtSetBreakAlloc(62); //可以定位到内存泄漏的位置
	_CrtDumpMemoryLeaks();
  	fun();
    _CrtDumpMemoryLeaks();  
}
```



# 基础

## SIMD和OpenMp

![](./img/HPC_1.png)

SIMD: 一条命令执行多个数据的运算

![](./img/HPC_2.png)

openMP:数据分到各个核运算

# code optimization

## Register and Cache

![](./img/HPC_3.png)register 

- register 寄存器
- latency 延迟 读写时间
- bandwidth 带宽 每次处理的数据大小

```c++
//利用cache加速 (空间局部性)

for(size_t i = 0; i < n; ++i)
{
    for(size_t j = 0; j < n; ++j)
        c[i][j] = A[i][j] + B[i][j];
}

for(size_t j = 0; j < n; ++j)
{
    for(size_t i = 0; i < n; ++i)
        c[i][j] = A[i][j] + B[i][j];
}

//利用k'j
//i-j循环更快 j的访问是连续的 数据会先放到cache cache的利用率会更高

```

## 局部性原理

- 时间局部性：访问的内存位置可能会再次访问
- 空间局部性：访问的内存位置，其附近的位置可能再次访问

```c++
for(size_t i = 0; i < n; ++i)
{
    for(size_t j = 0; j < n; ++j)
        c[i][j] = A[i][k] + B[i][j];
}

//优化 
for(size_t i = 0; i < n; ++i)
{
    int temp = A[i][k]
    for(size_t j = 0; j < n; ++j)
        c[i][j] = temp + B[i][j];
}

//复用数据提高时间局部性 简单的案列编译器会直接优化
```



# 编译和cmake

- 多文件编译：将多个代码文件编译成二进制文件 最后再链接
- 库：
    - 静态库：相等于直接把代码插入到可执行文件中，只需要一个文件即可执行
    - 动态库：可执行文件被加载时会读取指定目录中的dll文件，加载到内存中去，函数被调用时会跳转到相应的地址上
- 头文件：预处理阶段会将头文件的声明替换到实现文件中

## 使用第三方库

- 纯头文件引入：只需将头文件通过include_directoris包含进来。函数实现再头文件里，编译时间长
- 子模块引入：作为CMake子模块映入，通过add_subdirectory

```
fmtlib/fmt - 格式化库，提供 std::format 的替代品
gabime/spdlog - 能适配控制台，安卓等多后端的日志库
ericniebler/range-v3 - C++20 ranges 库就是受到他启发
g-truc/glm - 模仿 GLSL 语法的数学矢量/矩阵库
abseil/abseil-cpp - 旨在补充标准库没有的常用功能
bombela/backward-cpp - 实现了 C++ 的堆栈回溯便于调试
google/googletest - 谷歌单元测试框架
google/benchmark - 谷歌性能评估框架
glfw/glfw - OpenGL 窗口和上下文管理
libigl/libigl - 各种图形学算法大合集
```

- 引用预安装的第三方库：通过find_package来寻找。CMake认为一个包可以提供多个库，又称组件

```cmake
find_package(fmt REQUIRED)
target_link_libraries(a.out PUBLIC fmt::fmt) #使用fmt中的fmt组件
```



# RAII和智能指针

## c++更新

- c++14的lambda运行用auto自动推断类型
- c++17 CTAD(compile-time argument deduction, 编译器参数推断): `vector v = {1,2,3,4};`
- c++20 引入模块、format、协程
- c++ 20 允许函数参数为自动推断(void fun(auto&& v))



## RAII(Resource Acquisition Is Initialization)

RAII: 资源获取即初始化，资源释放即销毁。对象推出后自动销毁，具有异常安全性



# 模板元编程和函数式

`__PRETTY_FUNCTION__`可以打印当前的函数名

## 模板

- 模板函数可以自动推导参数类型
- 对某一类型进行特化

```c++
template <class T>
T twice(T t){
    return t*12;
}

std::string twice(std::string t){
    return t+t;
}

int main()
{
	twice("hello"); //报错 会去调用twice<char*>("hello");
}
```



- template <class T = int> 可以设置默认参数类型
- template <int N> 整数也可作为模板参数 N将作为编译期常量
- template <typename T> sum(const std::vector<T>& arr); 模板参数部分特化

### 模板的特性

- 模板会在编译时确定实例

- 如果不调用，不会检测语法错误
- **注意：** 编译器对模板的编译时堕性的。将声明放在a.h，实现放在a.cpp里面，a.cpp里没有调用也不会有实例，所以main.cpp里只看到声明从而出错。解决方案：可以在a.pp显示实例化。

## 自动推导

- typeid推导类型时，类型需具有虚函数才能准确推导

- decltype(变量名) 获取变量定义时候的类型- 。
- decltype(func()) p = func() 类似完美转发，还会知晓类型的引用定义

## 函数式

函数可以为另一个函数的参数

```c++
void call_twice(void func(int))
{
    func(0);
    func(1);
}

int main()
{
    call_twice(say_hello);
    return 0;
}
```

lambda表达式作为返回值

```c++
auto make_twice(){
	return [](int n){
		return n*2;
	}
}

int main(){
	auto twice = make_twice();
    call_twice(twice);
    return 0;
}

//lambad+模板 参数自动推导
//带auto参数的lamda表达式和模板函数一样，同样有惰性和多次编译的特性

template <typename Func>
void call_twice(Func const& func)
{
    std::cout << func(3.14f) << std::endl;
    std::cout << func(21) << std::endl;
}

int main()
{
    auto twice = [](auto n){
        return n*2;
    };
    call_twice(twice);
    return 0;
}


```

