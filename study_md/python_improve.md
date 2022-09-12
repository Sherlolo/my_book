# python_improve

标签（空格分隔）： study

---

# 基础扩展


## 常见基础语法

```

id(a) #查看变量a的地址

#多行语句
total = item_one + \
        item_two + \
        item_three
        
import sys; x = 'runoob'; sys.stdout.write(x + '\n') #使用；分割行语句

#使用del删除对象
var1 = 10
del var2

#多个变量同时赋值
a, b = 1, 2

#计算字符串的表达式
eval(str)
```

## 数学函数

```
abs(x) #返回绝对值 
math.fabs(x) #返回绝对值 math库的函数 一定返回浮点型

math.exp(x) #e
pow(x,y) #x**y
sqrt(x) #x的平方根
```

## 迭代器和生成器

### 迭代器

```
list = [1, 2, 3, 4]
it = iter(list) # 创建迭代器对象
print(next(it)) # 输出迭代器的下一个元素

for x in it:
    print(x, end="");
    
#创建一个迭代器
class mynumbers:
    def __iter__(self):
        self.a = 1
        return self
    def __next__(self):
        x = self.a
        self.a += 1
        return x
```

### 生成器

使用了 yield 的函数被称为生成器（generator）。
生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。
在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。

```
import sys

def fibonacci(n): # 生成器函数
    a, b, counter = 0, 1, 0
    while True:
        if counter > n:
            return
        yield a
        a, b = b, a + b
        counter += 1

f = fibonacci(10) #f是一个生成器
print(next(f)) #输出f
```


### 打包zip

zip函数能够把多个可迭代对象打包成一个元组构成的可迭代对象，它返回了一个 zip 对象

```
#往往在循环迭代中使用
In [21]: for i, j, k in zip(L1, L2, L3):
   ....:     print(i, j, k)
   ....: 
a d h
b e i
c f j
```
## 函数

```
#较为标准的写法 加入传递参数和返回参数的类型的说明
def _find_classes(self, dir: str) -> Tuple[List[str], Dict[str, int]]:

```

### 函数传入参数

注意：
在 python 中，strings, tuples, 和 numbers(传入基础的数据类型) 是不可更改的对象，
list,dict 等则是可以修改的对象。


```
def change(a):
    print(id(a))
    a = 10 #这里a会创建一个新的临时变量
    print(id(a))
    
def changeme( mylist ):
   mylist.append([1,2,3,4]) #函数体外的数据将发生改变
   return
```


### 不定长参数

不定长参数的传入形式：

- 加了*号的以元组形式传入
- 加了**号的以字典类型传入

```
def printinfo(arg1, *vartuple):
    print(vartuple)
    
def printinfo( arg1, **vardict ):
   print ("输出: ")
   print (arg1)
   print (vardict)
```

### 匿名函数

python 使用 lambda 来创建匿名函数。所谓匿名，意即不再使用 def 语句这样标准的形式定义一个函数。

- lambda 只是一个表达式，函数体比 def 简单很多。
- lambda的主体是一个表达式，而不是一个代码块。仅仅能在lambda表达式中封装有限的逻辑进去。
- lambda 函数拥有自己的命名空间，且不能访问自己参数列表之外或全局命名空间里的参数。
- 虽然lambda函数看起来只能写一行，却不等同于C或C++的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。


```
# 可写函数说明
sum = lambda arg1, arg2: arg1 + arg2
 
# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```


## 异常


![image.png-78.3kB](http://static.zybuluo.com/ShowArcher/tx5tnjqg5wjwh4f2hkrogka7/image.png)

```
try:
    runoob()
except AssertionError as error:
    print(error)
else:
    try:
        with open('file.log') as file:
            read_data = file.read()
    except FileNotFoundError as fnf_error:
        print(fnf_error)
finally:
    print('这句话，无论异常是否发生都会执行。')
```

## 面向对象

### 类的专有方法


\__init__ : 构造函数，在生成对象时调用
\__del__ : 析构函数，释放对象时使用
\__repr__ : 打印，转换
\__setitem__ : 按照索引赋值
\__getitem__: 按照索引获取值或者迭代进行访问
\__len__: 获得长度
\__cmp__: 比较运算
\__call__: 函数调用
\__add__: 加运算
\__sub__: 减运算
\__mul__: 乘运算
\__truediv__: 除运算
\__mod__: 求余运算
\__pow__: 乘方
