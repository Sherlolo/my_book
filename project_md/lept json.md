# lept json

# 概要和预备知识

## 换行符和回车符的区别

 	首先介绍一下“回车”（carriage return,’\r’）和“换行”（line feed,’\n’）这两个概念的来历和区别。在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打10个字符。但是它有一个问题，就是打完一行换行的时候，要用去0.2秒，正好可以打两个字符。要是在这0.2秒里面，又有新的字符传过来，那么这个字符将丢失。于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做“回车”，告诉打字机把打印头定位在左边界；另一个叫做“换行”，告诉打字机把纸向下移一行。这就是“换行”和“回车”的来历，从它们的英语名字上也可以看出一二。 

​	后来，计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧：

- Unix 系统里，每行结尾只有“<换行>”，即“\n”；
- Windows系统里面，每行结尾是“<回车><换行>”，即“\r\n”；
- Mac系统里，每行结尾是“<回车>”，即“\r”。



# 实践经验

- 字符串深拷贝，最后str[len]='\0'
- 仔细思考传参的指针是指向一个局部变量还是分配一个新的内存。
- 初始化函数，考虑函数第一次运行的初值。
- memcpy（）的len是字节数



# 动态内存分配

主要考虑使用malloc和realloc.

```c++
void *malloc(size_t size);
```

malloc的作用是在内存的动态存储区中分配一个长度为size的连续空间。其参数是一个无符号整形数，返回值是一个指向所分配的连续存储域的起始地址的指针.

- 函数未能成功分配存储空间时会返回一个NULL指针，所以在调用时应该先判断是否为空

```c++
void *realloc (void *ptr, size_t new_size );
```

realloc函数用于修改一个原先已经分配的内存块的大小，可以使一块内存的扩大或缩小。当起始空间的地址为空，即*ptr = NULL,则同malloc。

- 扩大空间
  - 原来的尾后空间足够分配，则将原来的内存块的后边新增一块内存块。
  - 如果原先的内存尾部空间不足或原来的内存块无法改变大小。将重新分配一块新空间，并把原来内存的内容复制到新内存上去。
- 缩小空间：将内存块后的部分内存拿掉。



# 实现vector类似的容器

在实现vector相似的容器时，建议增长因子为1.5，vc上的增长因子即为1.5.

> 原因：
>
> 如果增长因子是2，那么每次重新分配时，必然不能用到之前分配的地址空间（因为2^n > 2^0 + 2^1 + ... 2^(n-1)），在缓存上不友好。所以建议增长因子最好是少于2，例如是1.5。



# 内存泄漏检查工具



## windows下的内存检测工具

在 Windows 下，可使用 Visual C++ 的 [C Runtime Library（CRT） 检测内存泄漏](https://msdn.microsoft.com/zh-cn/library/x98tx3cf.aspx)。

具体使用;

```
//定义头文件
#ifdef _WINDOWS
#define _CRTDBG_MAP_ALLOC
#include <crtdbg.h>
#endif

int main() {
#ifdef _WINDOWS
    _CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);
#endif
```



## Linux/OSX下的内存检测工具

在 Linux、OS X 下，我们可以使用 [valgrind](https://valgrind.org/) 工具（用 `apt-get install valgrind`、 `brew install valgrind`）。我们完全不用修改代码，只要在命令行执行：

```
$ valgrind --leak-check=full  ./leptjson_test
==22078== Memcheck, a memory error detector
==22078== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.
==22078== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info
==22078== Command: ./leptjson_test
==22078== 
--22078-- run: /usr/bin/dsymutil "./leptjson_test"
160/160 (100.00%) passed
==22078== 
==22078== HEAP SUMMARY:
==22078==     in use at exit: 27,728 bytes in 209 blocks
==22078==   total heap usage: 301 allocs, 92 frees, 34,966 bytes allocated
==22078== 
==22078== 2 bytes in 1 blocks are definitely lost in loss record 1 of 79
==22078==    at 0x100012EBB: malloc (in /usr/local/Cellar/valgrind/3.11.0/lib/valgrind/vgpreload_memcheck-amd64-darwin.so)
==22078==    by 0x100008F36: lept_set_string (leptjson.c:208)
==22078==    by 0x100008415: test_access_boolean (test.c:187)
==22078==    by 0x100001849: test_parse (test.c:229)
==22078==   
```

