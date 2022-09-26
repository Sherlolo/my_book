# Python_100        

[TOC]

----------

Tags： study

---
## 1.day1-day5
---
### 1.1 python简介
python中的注释：

- 单行注释 #开头
- 多行注释 三个引号开题 三个引号结尾
### 1.2 python语言元素
变量类型：

- 整型：Python中可以处理任意大小的整数（Python 2.x中有int和long两种类型的整数，但这种区分对Python来说意义不大，因此在Python 3.x中整数只有int这一种了），而且支持二进制（如0b100，换算成十进制是4）、八进制（如0o100，换算成十进制是64）、十进制（100）和十六进制（0x100，换算成十进制是256）的表示法。
- 浮点型：浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，浮点数除了数学写法（如123.456）之外还支持科学计数法（如1.23456e2）。
- 字符串型：字符串是以单引号或双引号括起来的任意文本，比如'hello'和"hello",字符串还有原始字符串表示法、字节字符串表示法、Unicode字符串表示法，而且可以书写成多行的形式（用三个单引号或三个双引号开头，三个单引号或三个双引号结尾）。
- 布尔型：布尔值只有True、False两种值，要么是True，要么是False，在Python中，可以直接用True、False表示布尔值（请注意大小写），也可以通过布尔运算计算出来（例如3 < 5会产生布尔值True，而2 == 1会产生布尔值False）。
- 复数型：形如3+5j，跟数学上的复数表示一样，唯一不同的是虚部的i换成了j。实际上，这个类型并不常用，大家了解一下就可以了。

变量命名

变量名由字母（广义的Unicode字符，不包括特殊字符）、数字和下划线构成，数字不能开头。
```
python 变量无需声明定义即可使用
c语言： int a; a = 10;
python: a = 10;

可以使用type()查看类型
int()：将一个数值或字符串转换成整数，可以指定进制。
float()：将一个字符串转换成浮点数。
str()：将指定的对象转换成字符串形式，可以指定编码。
chr()：将整数转换成该编码对应的字符串（一个字符）。
ord()：将字符串（一个字符）转换成对应的编码（整数）。
```
运算符

    * / % //	乘，除，模，整除
    ** 指数
    a += 2 正确
    a++ 错误

```
获取输入和输出
a = int(input('a = '))
b = int(input('b = '))
print('%d + %d = %d' % (a, b, a + b))
```

### 1.3分支结构和循环结构

分支结构
```
if x > 1:
    y = 3 * x - 5
elif x >= -1:
    y = x + 2
else:
    y = 5 * x + 3    
```

循环结构
```
sum = 0
for x in range(101):    //x不是局部变量 循环外依然存在
    sum += x
print(sum)

range(1, 101, 2)：可以用来产生1到100的奇数，其中2是步长，即每次数值递增的值。
range(100, 0, -2)：可以用来产生100到1的偶数，其中-2是步长，即每次数字递减的值。

for index,val in enumerate(array):
    print(str(index)+"--"+val)
    
for key, value in d.items():
    print(key, ':', value)
```

代码缩进：利用\对代码换行
    
    is_leap = year % 4 == 0 and year % 100 != 0 or \
          year % 400 == 0

## 2.day6 函数和模块

---

### 2.1 函数

- 函数声明
```
def fac(num):
def add(a=0, b=0, c=0):
def add(*args):
#args代表一个可变的参数 tuple函数中类型为tuple
```

- 函数调用
```
print(add(1, 2))
print(add(1, 2, 3))
# 传递参数时可以不按照设定的顺序进行传递
print(add(c=50, a=100, b=200))
```

- 用模块管理函数

从mudule.py里调用函数
```
from module1 import foo
# 输出hello, world!
foo()

from module2 import foo
# 输出goodbye, world!
foo()
```

从module.py里用类调用
```
import module1 as m1
import module2 as m2
m1.foo()
m2.foo()
```
module.py

为了import module时 不运行module里的运行代码
```
def bar():
    pass

# __name__是Python中一个隐含的变量它代表了模块的名字
# 只有被Python解释器直接执行的模块的名字才是__main__
if __name__ == '__main__':
    print('call foo()')
    foo()
    print('call bar()')
    bar()
```

- 变量的作用域

```
def foo():
    global a    #如果没有a 会创建一个全局变量a
    a = 200
    print(a)  # 200
if __name__ == '__main__':
    a = 100
    foo()
    print(a)  # 200
```

实际开发应尽量减少全局变量

## 3 day7 字符串和数据结构

---

### 3.1 字符串即相关操作

- 字符串介绍

```
s1 = 'hello, world!'
#使用r后 \不再表示转义
s1 = r'hello, world!\n'
s1 = 'hello ' * 3
print(s1) # hello hello hello 
s2 = 'world'
s1 += s2
print(s1) # hello hello hello world
```

- 字符串处理

```
str2 = 'hello world'
print(len(str2))
print(str2.title()) #每个单词首字母大写
print(str2.find('or'))
print(str2.startswith('he'))
print(str2.endswith('s'))
print(str2.endswith('ld'))
```

### 3.2格式化输出和同时遍历

```
a, b = 5, 10
print('%d * %d = %d'%(a, b, a * b))
#字符串提供的方式进行标签
print('{0}*{1} = {2}'.format(a, b, a*b))
print(f'{a}*{b}={a*b}')
print(f"我 {age} 岁了，明年我就{age + 1}岁了~")
print('{:.2f}'.format(3.141592653))  # 输出结果：3.14

##同时便利
name = ["a", "b", "c"]
score = [1,2,3]
d = {}
for n, s in zip(name, score):
    d[n]=s
print(d)

```

### 3.3 列表

- 列表的定义和使用

列表是一种一种结构化的、非标量类型。它是值的有序序列。可以通过索引进行标识。

```
list1 = [1, 3, 5, 7, 100]
list2 = ['hello'] * 3

# 通过enumerate函数处理列表之后再遍历可以同时获得元素索引和值
for index, elem in enumerate(list1):
    print(index, elem)
```

- 列表的添加和删除操作

```
list1 = [1, 2, 5, 7, 100]
#向列表末尾添加元素
list1.append(300)

#列表元素加入列表
list1.extend(200)

#向列表固定位置添加元素
list1.insert(1, 400)
#从指定位置删除元素
list1.pop(1)

if 2 in list1:
    list1.remove(2)
#清空列表
list1.clear()
```

- 列表的排序操作

```
#列表的排序操作 默认按字母顺序递增
#sorted会返回一个排序好的列表 而sort方法是对原列表更改
list1 = ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
list2 = sorted(list1)
list3 = sorted(list1, reverse = True)
list4 = sorted(list1, key = len)    #key代表关键字 表示按长度递增

list1.sort()
```

- 列表生成式和生成器

```
import sys
#生成式会占据一定的空间
f = [x for x in range(1, 10)]
print(f)
print(sys.getsizeof(f))
#生成器不占用空间 需要它时会花费一定的时间运算得到
f1 = (x for x in range(1, 10))
print(sys.getsizeof(f1))
for val in f1:
    print(val)
```

### 3.4 元组

用一个变量（对象）来存储多个数据,但元组内的元素不可更改
tuple

```
#元组
t = ('杨亿男', 18, True, '四川成都')
print(t)
print(t[1])

#元组转为列表
person = list(t)

#列表转为元组
list1 = ['apple', 'banana', 'orange']
tuple1 = tuple(list1)
print(tuple1)


#注意
('person') #会识别为一个字符串而不是元组
('person',) #才会识别为一个元组
```

### 3.5 集合

集合于数学上定义的集合类似，不可重复，自动排序

```
set1 = {1, 2, 12, 3, 7}
set2 = set(range(1,10))

#集合操作
#添加
set1.add(4)
set1.add(5)
set2.update([11, 12])
#删除元素
set1.discard(5)
set1.remove(4)


print('judge')
#交 并 差 对称差
print(set1 & set2)
print(set1 | set2)
print(set1 - set2)
print(set1 ^ set2)
#判读子集 返回True或False
print(set1 <= set2)
```

### 3.6 字典

字典是另一种可变容器模型,可以存储任意类型对象,每个元素都是由一个键和一个值组成的“键值对”.

```
#字典
score = {'罗浩':95, '白元芳':78}
#创建字典的构造器语法
items1 = dict(one = 1, two = 2, three = 3)

#添加元素
score['key1'] = value1
score.update(冷面=67, 李楠茜=250)
score.update(dict1) #把dict1的内容加到score

#删除元素
del score['key']
print(score.popitem()) #弹出最后一个元素
print(score)
print(score.pop('罗浩', 100))   #100 表示未找到‘罗浩’时返回的默认值
del score['罗浩']
score.clear()
print(score)

#遍历字典
files = {"ID": 111, "passport": "my passport", "books": [1,2,3]}

for key in files.keys():
    print("key:", key)

for value in files.values():
    print("value:", value)

for key, value in files.items():
    print("key:", key, ", value:", value)
    
    
#判断
key in dict
如果键在字典dict里返回true，否则返回false
```

eval() 去除引号进行计算

> join 连接

join()：    连接字符串数组。将字符串、元组、列表中的元素以指定的字符(分隔符)连接生成一个新的字符串

```
str = "-";
seq = ("a", "b", "c"); # 字符串序列
print str.join( seq );
#输出
a-b-c
''.join(Dict) 字典转字符
```

## 4 Day8 面向对象编程基础


关于面向对象编程的解释：

    "把一组数据结构和处理它们的方法组成对象（object），把相同行为的对象归纳为类（class），通过类的封装（encapsulation）隐藏内部细节，通过继承（inheritance）实现类的特化（specialization）和泛化（generalization），通过多态（polymorphism）实现基于对象类型的动态分派。"

对象是具体的概念 而类是抽象的概念

self 代表的是类的实例，代表当前对象的地址，而 self.class 则指向类。

- 创建和使用对象

```
#写在类的函数通常称为方法
class Student(object):
    #_init_是一个特殊的方法用于初始化操作
    #可以为对象创建属性
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def study(self, course_name):
        print('%s正在学习%s.'%(self.name, course_name))

    def watch_movie(self):
        if self.age < 18:
            print('%s只能观看<熊出没>.'%self.name)
        else:
            print('%s可以观看岛国爱情大片'%self.name)
     def __str__(self): #在输出类时会 自动调用str方法 输出字符串
        return '(%s,%s)'%(str(self.name),str(self.age))

#创建和使用对象
def main():
    #创建学生对象
    stu1 = Student('夏洛洛', 18)
    #给对象发送消息
    stu1.study('python程序设计')
    stu1.watch_movie()
    stu2 = Student('王大锤', 16)
    stu2.study('思想品德')
    stu2.watch_movie()
```

- 类的公开性(public)和私有性(private)

一般采用公开属性的类，而私有属性类采用两个下划线开头，具体实现利用名词更换进行。实际开发中很少使用到私有属性，所以一般采取单个下划线开头表示该属性是受保护的

_ 表示弱隐藏，可以引用，但不建议
__ 表示强隐藏，完全不可引用

```
一个私有属性具有保护性质的类
class Test:
    def __init__(self, foo):
        self.__foo = foo

    def __bar(self):
        print(self.__foo)
        print('__bar')

#显示缺少属性
def main():
    test = Test('hello')
    test.__bar()
    print(test.__foo)

#正常输出
def main():
    test = Test('hello')
    test._Test__bar()
    print(test._Test__foo)

if __name__ == '__main__':
    main()
```

## 5 Day9面向对象进阶

### 类的专有方法


\__init__ : 构造函数，在生成对象时调用
\__del__ : 析构函数，释放对象时使用
\__repr__ : 打印，转换
\__setitem__ : 按照索引赋值
\__getitem__: 按照索引获取值
\__len__: 获得长度
\__cmp__: 比较运算
\__call__: 函数调用
\__add__: 加运算
\__sub__: 减运算
\__mul__: 乘运算
\__truediv__: 除运算
\__mod__: 求余运算
\__pow__: 乘方


---

### 常见函数 hasattr isinstance

```
hasattr(object, name) #判断对象object里是否含有属性name

isinstance(object, classinfo) #如果对象的类型与参数二的类型（classinfo）相同则返回 True，否则返回 False。。
```


### 5.1 @property装饰器

@property装饰器 它把方法包装成属性，让方法可以以属性的形式被访问和调用。
使用getter（访问器）和setter（修改器）方法进行对应的操作 deleter

```
class Person(object):

    def __init__(self, name, age):
        self._name = name
        self._age = age

    #访问器 getter方法
    @property
    def name(self):
        return self._name

    #getter方法
    @property
    def age(self):
        return self._age

    #修改器 setter方法
    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋'%self.name)
        else:
            print('%s正在玩斗地主'%self.name)

def main():
    person = Person('莫迪生', 12)
    person.play()
    person.age = 22 #可以使用age也可以使用_age 只是age时会调用相应的方法
    person.play()   
    #person.name = 'xx' # error 并未用@property进行装饰

if __name__ == '__main__':
    main()
```

- __slot\__ 限定类

```
class Person(object):
    # 限定Person对象只能绑定_name, _age和_gender属性
    __slot__ =('_name', '_age', '_gender')

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

def main():
    person = Person('王大锤', 22)
    person.play()
    person._gender = '男'
    # AttributeError: 'Person' object has no attribute '_is_gay'
    # person._is_gay = True #进行限定后不能添加类
```

### 5.2 静态方法和类方法

采用@staticmethod修饰器可以调用类方法和静态方法
即静态方法是属于类的，而不是属于对象的(参数带有self)

```
from math import sqrt
#采取先判断 再创建类的方法
class Triangle(object):

    def __init__(self, a, b, c):
        self._a = a
        self._b = b
        self._c = c

    @staticmethod
    def is_valid(a, b, c):
        returne a + b > c and a + c > b and b + c > a
    
    def perimeter(self):
        return self._a + self._b + self._c

    def area(self):
        half = self.perimeter() / 2
        return sqrt(half * (half - self._a) * (half - self._b) *(half - self._c))
    
def main():
    a, b, c = 3, 4, 5
    #静态方法和类方法都是通过类来发信息调用的
    if Triangle.is_valid(a, b, c):
        t = Triangle(a, b, c)
        print(t.perimeter())
        #也可以通过给类发消息的方式
        #print(Triangle.perimeter(t))
        print(t.area())
        #print(Triangle.area(t))
    else:
        print('不能构成三角形')

if __name__ == '__main__':
    main()
```

- 类中定义类方法

```
from time import time, localtime, sleep

class Clock(object):

    def __init__(self, hour = 0, minute = 0, second = 0):
        self._hour = hour
        self._minute = minute
        self._second = second

    #在类中定义类方法 cls是表示自身类的参数
    @classmethod
    def now(cls):
        ctime = localtime(time())
        return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)

    def run(self):
        #走字
        self._second += 1
        if self._second == 60:
            self._second = 0
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour = (self._hour + 1)%24
    def show(self):
        return '%02d:%02d:%02d' % \
               (self._hour, self._minute, self._second)

def main():
    # 通过类方法创建对象并获取系统时间
    clock = Clock.now()
    while True:
        print(clock.show())
        sleep(1)
        clock.run()

if __name__ == '__main__':
    main()
```

### 5.3 类之间的关系

简单的说，类和类之间的关系有三种：is-a、has-a和use-a关系，分别对应继承或泛化、关联、依赖三种关系。

-   继承

```
#继承

class Person(object):
    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        print('%s正在愉快的玩耍'%self._name)

    def watch_av(self):
        if self._age >= 18:
            print('%s正在观看爱情动作片.' % self._name)
        else:
            print('%s只能观看《熊出没》.' % self._name)

#继承
class Student(Person): #继承person里的方法
    def __init__(self, name, age, grade):
        super().__init__(name, age) #可以通过super继承各种方法和属性 super相当于调用父类的init方法 python2用的是super(类,self)
        self._grade = grade

    @property
    def grade(self):
        return self._grade

    @grade.setter
    def grade(self, grade):
        self._grade = grade

    def study(self, course):
        print('%s的%s正在学习%s.'%(self._grade, self._name, course))

def main():
    stu = Student('王大锤', 15, '初三')
    stu.study('数学')
    stu.watch_av()

if __name__ == '__main__':
    main()
```

- 多态
对父类已有的方法给出新的实现版本，称为方法重写。通过abstractmethod方法完成
调用重写方法时，不同子类对象会表现出不同的行为 这就是多态。
```
from abc import ABCMeta, abstractmethod

class Pet(object, metaclass = ABCMeta):
    #宠物
    def __init__(self, nickname):
        self._nickname = nickname

    @abstractmethod
    def make_voice(self):
        #发出声音
        pass    #pass代表空 不执行

class Dog(Pet):
    def make_voice(self):
        print('%s: 汪汪汪...' % self._nickname)

class Cat(Pet):
    """猫"""

    def make_voice(self):
        print('%s: 喵...喵...' % self._nickname)

def main():
    pets = [Dog('旺财'), Cat('凯瑞'), Dog('大黄')]
    for pet in pets:
        pet.make_voice()
```

通过如上代码，Pet类处理成了一个抽象类，即不能创建对象的类。
可以通过abc模块的ABCMeta元类和abstractmethod包装器来达到抽象类的效果。

## 6 day10图形用户界面开发

- GUI应用

利用tkinter模块编写GUI,tkinter模块是python默认的GUI开发模块。另外wxPython、PyQt、PyGTK等模块都是不错的选择。

```
import tkinter
import tkinter.messagebox

#利用tkinter做一个简单的GUI应用
def main():
    flag = True

    #修改标签上的字
    def change_label_text():
        nonlocal flag   #nonlocal 用来声明外层的局部变量 global 用来声明全局变量。
        flag = not flag
        color, msg = ('red', 'hello, world!')\
            if flag else ('blue', 'Goodbye', 'world!')
        label.config(text = msg, fg = color)

    #确认退出
    def confirm_to_quit():
        if tkinter.messagebox.askokcancel('温馨提示', '确定要退出吗？'):
            top.quit()

    #创建顶层窗口
    top = tkinter.Tk()
    #设置窗口大小
    top.geometry('240x160')
    #设置窗口大小
    top.title('小游戏')
    #创建标签对象到顶层窗口
    label = tkinter.Label(top, text = 'Hello, world!', font = 'Arial -32', fg = 'red')
    label.pack(expand = 1)
    #创建一个装按钮的容器
    panel = tkinter.Frame(top)
    #创建按钮对象 通过command参数绑定事件回调函数
    button1 = tkinter.Button(panel, text = '修改', command = change_label_text)
    button1.pack(side = 'left')
    button2 = tkinter.Button(panel, text='退出', command=confirm_to_quit)
    button2.pack(side = 'left')
    panel.pack(side = 'bottom')
    #开启主事件循环
    tkinter.mainloop()

if __name__ == '__main__':
    main() 
```

注意：nonlocal用来声明外层的局部变量 global用来声明全局变量。

- 利用pygame进行游戏开发

```
from enum import Enum, unique
from math import sqrt
from random import randint

import pygame

@unique
class Color(Enum):
    '颜色'

    RED = (255, 0, 0)
    GREEN = (0, 255, 0)
    BLUE = (0, 0, 255)
    BLACK = (0, 0, 0)
    WHITE = (255, 255, 255)
    GRAY = (242, 242, 242)

    @staticmethod
    def random_color():
        '获得随机颜色'
        r = randint(0, 255)
        g = randint(0, 255)
        b = randint(0, 255)
        return (r, g, b)

class Ball(object):
    '球'
    def __init__(self, x, y, radius, sx, sy, color = Color.RED):
        self.x = x
        self.y = y
        self.radius = radius
        self.sx = sx
        self.sy = sy
        self.color = color
        self.alive = True

    def move(self, screen):
        '移动'
        self.x += self.sx
        self.y += self.sy
        #边界碰撞后 步长取反
        if self.x - self.radius <= 0:
            self.sx = abs(self.sx)
        if self.x + self.radius >= screen.get_width():
            self.sx = -abs(self.sx)
        if self.y - self.radius <= 0:
            self.sy = abs(self.sy)
        if self.y + self.radius >= screen.get_height():
            self.sy = -abs(self.sy)
    
    def eat(self, other):
        '吃其他球'
        if self.alive and other.alive and self != other:
            dx, dy = self.x - other.x, self.y - other.y
            distance = sqrt(dx ** 2 + dy ** 2)
            if distance < self.radius + other.radius \
                and self.radius > other.radius:
                other.alive = False
                self.radius = self.radius + int(other.radius * 0.146)
    def draw(self, screen):
        pygame.draw.circle(screen, self.color, (self.x, self.y), self.radius, 0)

def main():
    #定义球的容器
    balls = []
    pygame.init()
    screen = pygame.display.set_mode((800, 800))
    pygame.display.set_caption('大球吃小球_yyn')
    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            #处理鼠标事件
            if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                #获得鼠标位置
                x, y = event.pos
                radius = randint(10, 100)
                sx, sy = randint(-10, 10), randint(-10, 10)
                color = Color.random_color()
                #在鼠标位置创建一个球
                ball = Ball(x, y, radius, sx, sy, color)
                # 将球添加到列表容器中
                balls.append(ball)
        screen.fill((255, 255, 255))
        #取球并输出到屏幕
        for ball in balls:
            if ball.alive:
                ball.draw(screen)
            else:
                balls.remove(ball)
        pygame.display.flip()
        pygame.time.delay(50)
        #改变位置
        for ball in balls:
            ball.move(screen)
            #检测是否有吃球行为
            for other in balls:
                ball.eat(other)

if __name__ == '__main__':
    main()
```

@unique修饰器是为了使枚举类型值唯一 在本项目中即rgb值唯一不重复

-----

## 7 day11 

### 7.1 文件的处理

文件的打开可以利用python内置的open函数，再对文件进行相应的读写处理。

- 读写文件

|操作模式|具体含义|
|----|-------------------|
|'r'|只读|
|'w'|覆盖写|
|'x'|写入，如果文件存在回发送异常|
|'a'|追加写|
|'b'|二进制模式|
|'t'|文本模式|
|'+'|更新|

    ---:表示左对齐 :---: 表示居中对齐

- 文件夹处理

```
import os

os.makedirs(path) #创建文件夹
os.path.join(path,path) #生成路径 ‘/’链接
os.path.exists('hello.txt') #判断文件和文件夹是否存在
os.path.isdir(path) #是否文件夹
os.listdir(path) #列表形式打开文件夹
os.path.isfile(path) #是否文件
os.removedirs(path) #移除文件夹
os.remove(path) #移除文件
os.path.getsize(path) #获取文件大小

#对文件夹里面有内容的移除
import shutil
shutil.rmtree(path)
```

- 读写文件
```
def main():
    f = open('text.txt', 'r', encoding = 'utf-8')
    print(f.read())
    f.close()
```

在如上代码中没有异常处理，若发送异常可能导致程序崩溃

```
#异常处理
def main():
    f = None
    try:
        f = open('text.txt', 'r')
        print(f.read())

    except FileNotFoundError:
        print('无法打开指定的文件')
    except LookupError:
        print('编码错误')
    except UnicodeDecodeError:
        print('解码错误')
    finally:    #无论异常与否都会执行
        if f:
            f.close()
```

在python中，利用`try... except...`进行异常处理，而`finally`模块为总是执行代码块，通常将其用来做外部资源的释放

```
#上下文语法自动释放外部资源

def main():
    try:
        with open('text.txt', 'r', encoding = 'utf-8') as f:
            print(f.read())
    except FileNotFoundError:
        print('无法打开指定文件')
    except LookupError:
        print('指定了未知的编码')
    except UnicodeDecodeError:
        print('解码错误')
```

利用`with`关键字指定文件对象的上下文环境并在离开上下文环境时自动释放资源

- 逐行读取文件

```
#逐行读取文件
import time

def main():
    with open('text.txt', 'r', encoding='utf-8') as f:
        print(f.read())
    with open('text.txt', 'r', encoding='utf-8') as f:
        for line in f:
            print(line, end = '')
            time.sleep(0.5)
    print()

    #读取文件按行读取到列表中
    with open('text.txt', 'r', encoding='utf-8') as f:
        lines = f.readlines()
    print(lines)
```

- 写文件

注意其写法

```
from math import sqrt

def is_prime(n):
    '判断素数'
    assert n > 0 #断言
    for factor in range(2, int(sqrt(n)) + 1):
        if n % factor == 0:
            return False
    return True if n != 1 else False

def main():
    filenames = ('a.txt', 'b.txt', 'c.txt')
    fs_list = []
    try:
        for filename in filenames:
            fs_list.append(open(filename, 'w', encoding='utf-8'))
        for number in range(1, 10000):
            if is_prime(number):
                if number < 100:
                    fs_list[0].write(str(number) + '\n')
                elif number < 1000:
                    fs_list[1].write(str(number) + '\n')
                else:
                    fs_list[2].write(str(number) + '\n')
    except IOError as ex:
        print(ex)
        print('写文件错误')
    finally:
        for fs in fs_list:
            fs.close()
    print('操作完成')
```

如果要实现对图片的读写功能，建议采用二进制文件访问

### 7.2 json文件

json与xml一样作为标签文件。作为异构系统交换数据的事实标准。

json与pyhon中的字典类似，其对应关系如下：

|python|json|
|-------|--------|
|dict|object|
|list,tuple|array|
|str|string|
|int,float|number|
|True/False|true/false|
|None|null|

```
#读写json文件
import json

def main():
    mydict = {
        'name': 'faker',
        'age': 22,
        'qq': 123456,
        'friends': ['王大锤', '百元芳'],
        'cars':[
            {'brand': 'BYD', 'Max_spe3d': 300},
            {'brand': 'Audi', 'Max_speed': 350}
        ]
    }
    try:
        with open('data.json', 'w', encoding='utf-8') as fs:
            json.dump(mydict, fs)
    except IOError as e:
        print(e)
    print('保存数据完成')
    
import json
data = {"filename": "f1.txt", "create_time": "today", "size": 111}
with open("data.json", "w") as f:
    json.dump(data, f)

print("直接当纯文本读：")
with open("data.json", "r") as f:
    print(f.read())

print("用 json 加载了读：")
with open("data.json", "r") as f:
    new_data = json.load(f) #返回
print("字典读取：", new_data["filename"])

```

json模块主要有四个比较重要的函数，分别是：

- dump - 将Python对象按照JSON格式序列化到文件中
- dumps - 将Python对象处理成JSON格式的字符串
- load - 将文件中的JSON数据反序列化成对象
- loads - 将字符串的内容反序列化成Python对象

这里出现了两个概念，一个叫序列化，一个叫反序列化。自由的百科全书维基百科上对这两个概念是这样解释的：“序列化（serialization）在计算机科学的数据处理中，是指将数据结构或对象状态转换为可以存储或传输的形式，这样在需要的时候能够恢复到原先的状态，而且通过序列化的数据重新获取字节时，可以利用这些字节来产生原始对象的副本（拷贝）。与这个过程相反的动作，即从一系列字节中提取数据结构的操作，就是反序列化（deserialization）”。

## 8 day12正则表达式

利用正则表达式处理字符串，查找复杂规则的字符串

    详细见https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/12.%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%92%8C%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F.md

.	匹配任意字符	b.t	可以匹配bat / but / b#t / b1t等
\w	匹配字母/数字/下划线	b\wt	可以匹配bat / b1t / b_t等
但不能匹配b#t
\s	匹配空白字符（包括\r、\n、\t等）	love\syou	可以匹配love you
\d	匹配数字	\d\d	可以匹配01 / 23 / 99等
\b	匹配单词的边界	\bThe\b	
^	匹配字符串的开始	^The	可以匹配The开头的字符串
$	匹配字符串的结束	.exe$	可以匹配.exe结尾的字符串
\W	匹配非字母/数字/下划线	b\Wt	可以匹配b#t / b@t等
但不能匹配but / b1t / b_t等
\S	匹配非空白字符	love\Syou	可以匹配love#you等
但不能匹配love you
\D	匹配非数字	\d\D	可以匹配9a / 3# / 0F等
\B	匹配非单词边界	\Bio\B	
[]	匹配来自字符集的任意单一字符	[aeiou]	可以匹配任一元音字母字符
[^]	匹配不在字符集中的任意单一字符	[^aeiou]	可以匹配任一非元音字母字符
*	匹配0次或多次	\w*	
+	匹配1次或多次	\w+	
?	匹配0次或1次	\w?	
{N}	匹配N次	\w{3}	
{M,}	匹配至少M次	\w{3,}	
{M,N}	匹配至少M次至多N次	\w{3,6}	
|	分支	foo|bar	可以匹配foo或者bar
(?#)	注释		
(exp)	匹配exp并捕获到自动命名的组中		
(?<name>exp)	匹配exp并捕获到名为name的组中		
(?:exp)	匹配exp但是不捕获匹配的文本		
(?=exp)	匹配exp前面的位置	\b\w+(?=ing)	可以匹配I'm dancing中的danc
(?<=exp)	匹配exp后面的位置	(?<=\bdanc)\w+\b	可以匹配I love dancing and reading中的第一个ing
(?!exp)	匹配后面不是exp的位置		
(?<!exp)	匹配前面不是exp的位置		
*?	重复任意次，但尽可能少重复	a.*b
a.*?b	将正则表达式应用于aabab，前者会匹配整个字符串aabab，后者会匹配aab和ab两个字符串
+?	重复1次或多次，但尽可能少重复		
??	重复0次或1次，但尽可能少重复		
{M,N}?	重复M到N次，但尽可能少重复		
{M,}?	重复M次以上，但尽可能少重复		
说明： 如果需要匹配的字符是正则表达式中的特殊字符，那么可以使用\进行转义处理，例如想匹配小数点可以写成\.就可以了，因为直接写.会匹配任意字符；同理，想匹配圆括号必须写成\(和\)，否则圆括号被视为正则表达式中的分组。

在python中采用re库对正则表达式进行处理

```
#正则表达式
#验证输入用户名和qq号是否有效并给出提示
#用户名必须由字母、数字或下划线构成且长度在6~20个字符之间，QQ号是5~12的数字且首位不能为0

import re

def main():
    username = input('请输入用户名')
    qq = input('请输入qq号')
    m1 = re.match(r'[0-9a-zA-Z_]{6,20}$', username)
    if not m1:
        print('请输入有效的用户名')
    m2 = re.match(r'[1-9]\d{4,11}$', qq)
    if not m2:
        print('请输入有效的qq号')
    if m1 and m2:
        print("有效")

if __name__ == '__main__':
    main()
```

注意：上面在书写正则表达式时使用了“原始字符串”的写法（在字符串前面加上了r），所谓“原始字符串”就是字符串中的每个字符都是它原始的意义，说得更直接一点就是字符串中没有所谓的转义字符啦。因为正则表达式中有很多元字符和需要进行转义的地方，如果不使用原始字符串就需要将反斜杠写作\\，例如表示数字的\d得书写成\\d，这样不仅写起来不方便，阅读的时候也会很吃力。

```
#替换字符串中的不良内容
import re

def main():
    sentence = '你Y是傻叉吗？我操你大爷的. Fuck you.'
    purified = re.sub(r'[操肏艹]|fuck|sit|傻[比屄逼叉缺吊屌]|煞笔', '*', sentence, flags = re.IGNORECASE)
    print(purified)

main()
```

## 9 day13进程和线程

### 9.1 进程和线程的概念

进程就是操作系统中执行的一个程序，操作系统以进程为单位分配存储空间，每个进程都有自己的地址空间、数据栈以及其他用于跟踪进程执行的辅助数据，操作系统管理所有进程的执行，为它们合理的分配资源。进程可以通过fork或spawn的方式来创建新的进程来执行其他的任务，不过新的进程也有自己独立的内存空间，因此必须通过进程间通信机制（IPC，Inter-ProcessCommunication）来实现数据共享，具体的方式包括管道、信号、套接字、共享内存区等

一个进程还可以拥有多个并发的执行线索，简单的说就是拥有多个可以获得CPU调度的执行单元，这就是所谓的线程。由于线程在同一个进程下，它们可以共享相同的上下文，因此相对于进程而言，线程间的信息共享和通信更加容易。当然在单核CPU系统中，真正的并发是不可能的，因为在某个时刻能够获得CPU的只有唯一的一个线程，多个线程共享了CPU的执行时间。使用多线程实现并发编程为程序带来的好处是不言而喻的，最主要的体现在提升程序的性能和改善用户体验，今天我们使用的软件几乎都用到了多线程技术，这一点可以利用系统自带的进程监控工具（如macOS中的“活动监视器”、Windows中的“任务管理器”）来证实。

### 9.2 多进程

Unix和Linux操作系统上提供了fork()系统调用来创建进程，调用fork()函数的是父进程，创建出的是子进程，子进程是父进程的一个拷贝，但是子进程拥有自己的PID。fork()函数非常特殊它会返回两次，父进程中可以通过fork()函数的返回值得到子进程的PID，而子进程中的返回值永远都是0。Python的os模块提供了fork()函数。由于Windows系统没有fork()调用，因此要实现跨平台的多进程编程，可以使用multiprocessing模块的Process类来创建子进程，而且该模块还提供了更高级的封装，例如批量启动进程的进程池（Pool）、用于进程间通信的队列（Queue）和管道（Pipe）等。

```
from multiprocessing import Process
from os import getpid
from random import randint
from time import time, sleep

def download_task(filename):
    print('启动下载进程，进程号[%d].' % getpid())
    print('开始下载%s...' % filename)
    time_to_download = randint(5, 10)
    sleep(time_to_download)
    print('%s下载完成! 耗费了%d秒' % (filename, time_to_download))

def main():
    start = time()
    p1 = Process(target = download_task, args = ('python学习.pdf', ))
    p1.start()
    p2 = Process(target = download_task, args = ('Decade.avi', ))
    p2.start()
    p1.join()
    p2.join()
    end = time()
    print('总共耗费了%.2f秒.' % (end - start))

if __name__ == '__main__':
    main()
```

      通过Process类创建了进程对象，通过target参数我们传入一个函数来表示进程启动后要执行的代码，后面的args是一个元组，它代表了传递给函数的参数。Process对象的start方法用来启动进程，而join方法表示等待进程执行结束。运行上面的代码可以明显发现两个下载任务“同时”启动了，而且程序的执行时间将大大缩短，不再是两个任务的时间总和。
      这样做可以提示程序的执行时间

### 9.3 多进程通信

```
from multiprocessing import Process
from time import sleep
counter = 0

def sub_task(string):
    global counter
    while counter < 10:
        print(string, end='', flush=True) #flush=true设置后 prin语句结束后会立即将内容打印到屏幕上
        counter += 1
        sleep(0.01)

def main():
    Process(target=sub_task, args=('Ping', )).start()
    Process(target=sub_task, args=('Pong', )).start()

if __name__ == '__main__':
    main()
```
      最后的结果是Ping和Pong各输出了10个，这是因为每个子进程有自己独立的内存空间，这也就意味着两个子进程中各有一个counter变量。
      要解决这个问题比较简单的办法是使用multiprocessing模块中的Queue类，它是可以被多个进程共享的队列，底层是通过管道和信号量（semaphore）机制来实现的，有兴趣的读者可以自己尝试一下。

### 9.4 多线程

      在Python早期的版本中就引入了thread模块（现在名为_thread）来实现多线程编程，然而该模块过于底层，而且很多功能都没有提供，因此目前的多线程开发我们推荐使用threading模块，该模块对多线程编程提供了更好的面向对象的封装。我们把刚才下载文件的例子用多线程的方式来实现一遍。

```
from random import randint
from threading import Thread
from time import time, sleep

def download(filename):
    print('开始下载%s...' % filename)
    time_to_download = randint(5, 10)
    sleep(time_to_download)
    print('%s下载完成! 耗费了%d秒' % (filename, time_to_download))

def main():
    start = time()
    t1 = Thread(target=download, args=('Python从入门到住院.pdf',))
    t1.start()
    t2 = Thread(target=download, args=('Peking Hot.avi',))
    t2.start()
    t1.join()
    t2.join()
    end = time()
    print('总共耗费了%.3f秒' % (end - start))

if __name__ == '__main__':
    main()
```

本着万物皆对象的方法，可以使用对象进行封装

```
from random import randint
from threading import Thread
from time import time, sleep

class DownloadTask(Thread):

    def __init__(self, filename):
        super().__init__()
        self._filename = filename

    def run(self):
        print('开始下载%s...' % self._filename)
        time_to_download = randint(5, 10)
        sleep(time_to_download)
        print('%s下载完成! 耗费了%d秒' % (self._filename, time_to_download))

def main():
    start = time()
    t1 = DownloadTask('Python从入门到住院.pdf')
    t1.start()
    t2 = DownloadTask('Peking Hot.avi')
    t2.start()
    t1.join()
    t2.join()
    end = time()
    print('总共耗费了%.2f秒.' % (end - start))

if __name__ == '__main__':
    main()
```

### 9.5 多线程访问共享变量

```
#对资源进行限制访问 互斥访问
from time import sleep
from threading import Thread, Lock


class Account(object):

    def __init__(self):
        self._balance = 0
        self._lock = Lock()

    def deposit(self, money):
        # 先获取锁才能执行后续的代码
        self._lock.acquire()    #v
        try:
            new_balance = self._balance + money
            sleep(0.01)
            self._balance = new_balance
        finally:
            # 在finally中执行释放锁的操作保证正常异常锁都能释放
            self._lock.release() #p

    @property
    def balance(self):
        return self._balance


class AddMoneyThread(Thread):

    def __init__(self, account, money):
        super().__init__()
        self._account = account
        self._money = money

    def run(self):
        self._account.deposit(self._money)


def main():
    account = Account()
    threads = []
    for _ in range(100):
        t = AddMoneyThread(account, 1)
        threads.append(t)
        t.start()
    for t in threads:
        t.join()
    print('账户余额为: ￥%d元' % account.balance)


if __name__ == '__main__':
    main()
```

      类似pv操作，实现对临界资源的互斥访问

### 9.6 多进程和多线程

      无论是多进程还是多线程，只要数量一多，效率肯定上不去，为什么呢？我们打个比方，假设你不幸正在准备中考，每天晚上需要做语文、数学、英语、物理、化学这5科的作业，每项作业耗时1小时。如果你先花1小时做语文作业，做完了，再花1小时做数学作业，这样，依次全部做完，一共花5小时，这种方式称为单任务模型。如果你打算切换到多任务模型，可以先做1分钟语文，再切换到数学作业，做1分钟，再切换到英语，以此类推，只要切换速度足够快，这种方式就和单核CPU执行多任务是一样的了，以旁观者的角度来看，你就正在同时写5科作业。
      但是，切换作业是有代价的，比如从语文切到数学，要先收拾桌子上的语文书本、钢笔（这叫保存现场），然后，打开数学课本、找出圆规直尺（这叫准备新环境），才能开始做数学作业。操作系统在切换进程或者线程时也是一样的，它需要先保存当前执行的现场环境（CPU寄存器状态、内存页等），然后，把新任务的执行环境准备好（恢复上次的寄存器状态，切换内存页等），才能开始执行。这个切换过程虽然很快，但是也需要耗费时间。如果有几千个任务同时进行，操作系统可能就主要忙着切换任务，根本没有多少时间去执行任务了，这种情况最常见的就是硬盘狂响，点窗口无反应，系统处于假死状态。所以，多任务一旦多到一个限度，反而会使得系统性能急剧下降，最终导致所有任务都做不好。
      可以把任务分为计算密集型和I/O密集型。计算密集型任务的特点是要进行大量的计算，消耗CPU资源，比如对视频进行编码解码或者格式转换等等，这种任务全靠CPU的运算能力，虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低。计算密集型任务由于主要消耗CPU资源，这类任务用Python这样的脚本语言去执行效率通常很低，最能胜任这类任务的是C语言，我们之前提到过Python中有嵌入C/C++代码的机制。
      除了计算密集型任务，其他的涉及到网络、存储介质I/O的任务都可以视为I/O密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待I/O操作完成（因为I/O的速度远远低于CPU和内存的速度）。对于I/O密集型任务，如果启动多任务，就可以减少I/O等待时间从而让CPU高效率的运转。有一大类的任务都属于I/O密集型任务，这其中包括了我们很快会涉及到的网络应用和Web应用。
      
    现代操作系统对I/O操作的改进中最为重要的就是支持异步I/O。如果充分利用操作系统提供的异步I/O支持，就可以用单进程单线程模型来执行多任务，这种全新的模型称为事件驱动模型。Nginx就是支持异步I/O的Web服务器，它在单核CPU上采用单进程模型就可以高效地支持多任务。在多核CPU上，可以运行多个进程（数量与CPU核心数相同），充分利用多核CPU。用Node.js开发的服务器端程序也使用了这种工作模式，这也是当下并发编程的一种流行方案。

在Python语言中，单线程+异步I/O的编程模型称为协程，有了协程的支持，就可以基于事件驱动编写高效的多任务程序。协程最大的优势就是极高的执行效率，因为子程序切换不是线程切换，而是由程序自身控制，因此，没有线程切换的开销。协程的第二个优势就是不需要多线程的锁机制，因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不用加锁，只需要判断状态就好了，所以执行效率比多线程高很多。如果想要充分利用CPU的多核特性，最简单的方法是多进程+协程，既充分利用多核，又充分发挥协程的高效率，可获得极高的性能。关于这方面的内容，在后续的课程中会进行讲解。

将耗时间的任务放到线程中以获得更好的用户体验。

```
import time
import tkinter
import tkinter.messagebox
from threading import Thread


def main():

    class DownloadTaskHandler(Thread):

        def run(self):
            time.sleep(10)
            tkinter.messagebox.showinfo('提示', '下载完成!')
            # 启用下载按钮
            button1.config(state=tkinter.NORMAL)

    def download(): #将耗时间的下载任务放在独立的线程中
        # 禁用下载按钮
        button1.config(state=tkinter.DISABLED)
        # 通过daemon参数将线程设置为守护线程(主程序退出就不再保留执行)
        # 在线程中处理耗时间的下载任务
        DownloadTaskHandler(daemon=True).start()

    def show_about():
        tkinter.messagebox.showinfo('关于', '作者: 骆昊(v1.0)')

    top = tkinter.Tk()
    top.title('单线程')
    top.geometry('200x150')
    top.wm_attributes('-topmost', 1)

    panel = tkinter.Frame(top)
    button1 = tkinter.Button(panel, text='下载', command=download)
    button1.pack(side='left')
    button2 = tkinter.Button(panel, text='关于', command=show_about)
    button2.pack(side='right')
    panel.pack(side='bottom')

    tkinter.mainloop()


if __name__ == '__main__':
    main()
```

将耗时间的任务放在一个独立的线程中，这样就不会因为执行耗时间的任务阻塞了主线程的运行

多进程的应用，我们可以将一个任务拆分为多个进程进行处理

```
#使用多进程处理计算过程 分成8个进程
from multiprocessing import Process, Queue
from random import randint
from time import time

def task_handler(curr_list, result_queue):
    total = 0
    for number in curr_list:
        total += number
    result_queue.put(total)

def main():
    process = []
    number_list = [x for x in range(1, 100000001)]
    result_queue = Queue()
    index = 0
    # 启动8个进程将数据切片后运算
    for _ in range(8):
        p = Process(target = task_handler, args = (number_list[index: index + 12500000],result_queue))
        index += 12500000
        process.append(p)
        p.start()

    #记录运行时间
    start = time()
    for p in process:
        p.join()

    total = 0
    while not result_queue.empty():
        total += result_queue.get()

    print(total)
    end = time()
    print('Execution time: ', (end - start), 's', sep = '')


if __name__ == '__main__':
    main()
```

## 10 day14网络编程入门

### 10.1 TCP/IP模型

      所谓“协议”就是通信计算机双方必须共同遵从的一组约定，例如怎样建立连接、怎样互相识别等，网络协议的三要素是：语法、语义和时序。构成我们今天使用的Internet的基础的是TCP/IP协议族，所谓协议族就是一系列的协议及其构成的通信模型，我们通常也把这套东西称为TCP/IP模型。与国际标准化组织发布的OSI/RM这个七层模型不同，TCP/IP是一个四层模型，也就是说，该模型将我们使用的网络从逻辑上分解为四个层次，自底向上依次是：网络接口层、网络层、传输层和应用层.

TCP全称传输控制协议，它是基于IP提供的寻址和路由服务而建立起来的负责实现端到端可靠传输的协议，之所以将TCP称为可靠的传输协议是因为TCP向调用者承诺了三件事情：

- 数据不传丢不传错（利用握手、校验和重传机制可以实现）。
- 流量控制（通过滑动窗口匹配数据发送者和接收者之间的传输速度）。
- 拥塞控制（通过RTT时间以及对滑动窗口的控制缓解网络拥堵)。

### 10.2 基于HTTP协议的网络资源访问

HTTP是超文本传输协议（Hyper-Text Transfer Proctol）的简称，维基百科上对HTTP的解释是：超文本传输协议是一种用于分布式、协作式和超媒体信息系统的应用层协议，它是万维网数据通信的基础，设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法，通过HTTP或者HTTPS（超文本传输安全协议）请求的资源由URI（统一资源标识符）来标识。关于HTTP的更多内容，我们推荐阅读阮一峰老师的《HTTP 协议入门》，简单的说，通过HTTP我们可以获取网络上的（基于字符的）资源，开发中经常会用到的网络API（有的地方也称之为网络数据接口）就是基于HTTP来实现数据传输的。

json、xml都是数据交换语言，而json相较于xml更为直观方便。

### 10.3 requests库

requests是一个基于HTTP协议来使用网络的第三库，其官方网站有这样的一句介绍它的话：“Requests是唯一的一个非转基因的Python HTTP库，人类可以安全享用。”简单的说，使用requests库可以非常方便的使用HTTP，避免安全缺陷、冗余代码以及“重复发明轮子”

```
from time import time
from threading import Thread
import requests
#通过requests库下载图片

# 继承Thread类创建自定义的线程类
class DownloadHanlder(Thread):

    def __init__(self, url):
        super().__init__()
        self.url = url

    def run(self):
        filename = self.url[self.url.rfind('/') + 1:]
        resp = requests.get(self.url)
        with open('/Users/Hao/' + filename, 'wb') as f:
            f.write(resp.content)


def main():
    # 通过requests模块的get函数获取网络资源
    # 下面的代码中使用了天行数据接口提供的网络API
    # 要使用该数据接口需要在天行数据的网站上注册
    # 然后用自己的Key替换掉下面代码的中APIKey即可
    resp = requests.get(
        'http://api.tianapi.com/meinv/?key=APIKey&num=10')
    # 将服务器返回的JSON格式的数据解析为字典
    data_model = resp.json()
    for mm_dict in data_model['newslist']:
        url = mm_dict['picUrl']
        # 通过多线程的方式实现图片下载
        DownloadHanlder(url).start()
if __name__ == '__main__':
    main()

```

### 10.4 套接字编程

套接字这个词对很多不了解网络编程的人来说显得非常晦涩和陌生，其实说得通俗点，套接字就是一套用C语言写成的应用程序开发库，主要用于实现进程间通信和网络编程，在网络应用开发中被广泛使用。在Python中也可以基于套接字来使用传输层提供的传输服务，并基于此开发自己的网络应用。实际开发中使用的套接字可以分为三类：流套接字（TCP套接字）、数据报套接字和原始套接字。

如下通过套接字实现时间的传输

```
#提供时期的服务器
#利用tcp套接字

from socket import socket, SOCK_STREAM, AF_INET
from datetime import datetime


def main():
    # 1.创建套接字对象并指定使用哪种传输服务
    # family=AF_INET - IPv4地址
    # family=AF_INET6 - IPv6地址
    # type=SOCK_STREAM - TCP套接字
    # type=SOCK_DGRAM - UDP套接字
    # type=SOCK_RAW - 原始套接字
    server = socket(family=AF_INET, type=SOCK_STREAM)
    # 2.绑定IP地址和端口(端口用于区分不同的服务)
    # 同一时间在同一个端口上只能绑定一个服务否则报错
    server.bind(('192.168.0.106', 15789))
    # 3.开启监听 - 监听客户端连接到服务器
    # 参数512可以理解为连接队列的大小
    server.listen(512)
    print('服务器启动开始监听...')
    while True:
        # 4.通过循环接收客户端的连接并作出相应的处理(提供服务)
        # accept方法是一个阻塞方法如果没有客户端连接到服务器代码不会向下执行
        # accept方法返回一个元组其中的第一个元素是客户端对象
        # 第二个元素是连接到服务器的客户端的地址(由IP和端口两部分构成)
        client, addr = server.accept()
        print(str(addr) + '连接到了服务器.')
        # 5.发送数据
        client.send(str(datetime.now()).encode('utf-8'))
        # 6.断开连接
        client.close()


if __name__ == '__main__':
    main()
```

## 11 网络爬虫

![方法](http://static.zybuluo.com/ShowArcher/g30z12oq2l8dbmv82pdnsff5/image.png)

![属性](http://static.zybuluo.com/ShowArcher/gvcbsbxqkvnyyco94btd1ab8/image.png)

![http][1]



### request参数

    requests.request(method, url, **kwargs)
    method:请求方式,包括'GET','HEAD'等

下面将介绍参数的使用
1. params 字典或字节序列,作为参数增加到url中

![image.png-84.5kB][2]


2.data 字典.字节序列或文件对象,作为reques的内容

![image.png-98kB][3]


3. json json格式的数据作为request的内容
![image.png-44.5kB][4]


4. headers 字典 http定制头
控制访问的浏览器

![image.png-59.5kB][5]


5. proxies 字典类型,设定访问的代理服务器
防止ip被追踪或被隔离
![image.png-81.6kB][6]



![image.png-196.8kB][7]


![image.png-211.6kB][8]


![image.png-325.2kB][9]


```
soup1 = BeautifulSoup(<html>data<html>, 'html.parser')      #直接打开html
soup2 = BeautifulSoup(open(D:/demo.html), 'html.parser')    #以文件形式打开
```

beautifulsoup的解析器有'html.parser'.'lxml'.'xml'.'html5lib'
![image.png-329.9kB][10]


### beautiful解析html
![image.png-105.8kB][11]


### 遍历标签树

![image.png-339kB][12]


![image.png-235.6kB][13]


![image.png-428.2kB][14]


 >beautifulsoup采用的编码时utf-8

 ```
 html的注释形式
 <!--data-->
 ```


 ### 消息格式


 ![image.png-551.7kB][15]


### 查找消息文件

![image.png-763.1kB][16]


查找的案例
![image.png-118kB][17]


![image.png-509.6kB][18]


![image.png-462.7kB][19]


  ![image.png-534.5kB][20]


 ![image.png-484.9kB][21]


[1]: http://static.zybuluo.com/ShowArcher/2g8rr82enguzcbgwge8oeh1h/image.png
[2]: http://static.zybuluo.com/ShowArcher/ijo0be7xiw5cza2on4fu1fye/image.png
[3]: http://static.zybuluo.com/ShowArcher/w3c38017la6yglk294nzsfzm/image.png
[4]: http://static.zybuluo.com/ShowArcher/n8e3f0vs3obm3h5vwhd4r1ll/image.png
[5]: http://static.zybuluo.com/ShowArcher/k2137u98g7lmfytu8kt6upce/image.png
[6]: http://static.zybuluo.com/ShowArcher/nzqi72nywrvqxuc7iav9a6hd/image.png
[7]: http://static.zybuluo.com/ShowArcher/yjt9tv0qm4fbh50wq54w77lk/image.png
[8]: http://static.zybuluo.com/ShowArcher/5jj4ydlzzyc4zfaofjreec2g/image.png
[9]: http://static.zybuluo.com/ShowArcher/l3ilqjq5355nidu2n9khnrpj/image.png
[10]: http://static.zybuluo.com/ShowArcher/bjg2uem2jftlt96mezw6se7i/image.png
[11]: http://static.zybuluo.com/ShowArcher/4hd1kay7sth3k0wvezju4mgv/image.png
[12]: http://static.zybuluo.com/ShowArcher/butra0sm49cteahyrii3cppg/image.png
[13]: http://static.zybuluo.com/ShowArcher/b75hgw88tqjmjn4aqtmqpupe/image.png
[14]: http://static.zybuluo.com/ShowArcher/uvbp2dn361x4uktl830oxc2l/image.png
[15]: http://static.zybuluo.com/ShowArcher/p6f1qisniwtbm1i9jrto17jp/image.png
[16]: http://static.zybuluo.com/ShowArcher/mty5kygppyzx1q5vtoezzncj/image.png
[17]: http://static.zybuluo.com/ShowArcher/y6kh61cfbfwmy3w7rwptgb34/image.png
[18]: http://static.zybuluo.com/ShowArcher/h5xk2xyzwlqppmump6oa4fgh/image.png
[19]: http://static.zybuluo.com/ShowArcher/h5xk2xyzwlqppmump6oa4fgh/image.png
[20]: http://static.zybuluo.com/ShowArcher/7f8t34w0rotlbya7n9mo6ua8/image.png
[21]: http://static.zybuluo.com/ShowArcher/wvkif66zxqf5fmlrw774ekvw/image.png\

## 12 生成器yield


yield生成器，可以理解为return,在运行到它时会返回，下次调用会继续在函数中往下运行

```
def need_return():
    for item in range(5):
        if item % 2 == 0:
            print("我要扔出去一个 item=%d 了" % item)
            yield item  # 这里就会返回给下面的 for 循环
            print("我又回到里面了")

for i in need_return():
    print("我在外面接到了一个 item=%d\n" % i)
```

>应用场景

```
#如下程序中range(1000)会占用较大的内存 改成yiled会节约内存空间
for item in range(1000):
    if item % 2 == 0:

#如下 会打印出所需的数据
def need_return():
    tmp = 2
    for item in range(300):
        if item == tmp:
            yield item
for i in need_return():
    print(i)
```

## 13 装饰器

### 定义
    Python的装饰器本质上是一个嵌套函数，它接受被装饰的函数(func)作为参数，并返回一个包装过的函数。这样我们可以在不改变被装饰函数的代码的情况下给被装饰函数或程序添加新的功能。Python的装饰器广泛应用于缓存、权限校验(如django中的@login_required和@permission_required装饰器)、性能测试(比如统计一段程序的运行时间)和插入日志等应用场景。有了装饰器，我们就可以抽离出大量与函数功能本身无关的代码，增加一个函数的重用性。
    
    应用：对已写好的函数，可以添加其新的功能

闭包

    闭包是Python编程一个非常重要的概念。如果一个外函数中定义了一个内函数，且内函数体内引用到了体外的变量，这时外函数通过return返回内函数的引用时，会把定义时涉及到的外部引用变量和内函数打包成一个整体（闭包）返回。此时的临时变量会保留。


### 实例

比如要测试一个函数的运行时间
```
import time
def foo():
    print('in foo()')

def timeit(func):
    def wrapper():
        start = time.time()
        func()
        end = time.time()
        print('used:', end - start)
    # 将包装后的函数返回
    return wrapper
    
#运行方式1  这种方式需将原来的foo() -> timeit(foo)
timeit(foo)

#运行方式2
foo = timeit(foo)#传入的参数foo为原函数名，变量foo为新变量不是原函数
foo()

#运行方式3
@timeit #加入装饰器后 foo()想当于foo = timeit(foo)
def foo():
    print('in foo()')
    
foo() 
```

多层装饰

```
def makebold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped

def makeitalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped

@makebold
@makeitalic
def hello():
    return "hello world"

print hello() ## 返回 <b><i>hello world</i></b>
```

### 特性

```
def outer(x):
    a = x

    def inner(y):
        b = y
        print(a+b)

    return inner

f1 = outer(1) # 返回inner函数对象
f1(10) # 相当于inner(10)。输出11
```

此时的局部变量a会因闭包的原因而保留


## 14 zip打包迭代器

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

## 15 argarse封装

### 使用步骤

1. import argparse 首先导入模块
2. parser = argparse.ArgumentParser（） 创建一个解析对象
3. parser.add_argument() 向该对象中添加你要关注的命令行参数和选项
4. parser.parse_args() 进行解析

### parser = argparse.ArgumentParser（） 创建对象
一般不修改参数

- description - 命令行帮助的开始文字，大部分情况下，我们只会用到这个参数
- epilog - 命令行帮助的结尾文字
- prog - (default: sys.argv[0])程序的名字，一般不需要修改，另外，如果你需要在help中使用到程序的名字，可以使用%(prog)s
- prefix_chars - 命令的前缀，默认是-，例如-f/–file。有些程序可能希望支持/f这样的选项，可以使用prefix_chars=”/”
fromfile_prefix_chars - (default: None)如果你希望命令行参数可以从文件中读取，就可能用到。例如，如果fromfile_prefix_chars=’@’,命令行参数中有一个为”@args.txt”，args.txt的内容会作为命令行参数
- add_help - 是否增加-h/-help选项 (default: True)，一般help信息都是必须的，所以不用设置啦。
- parents - 类型是list，如果这个parser的一些选项跟其他某些parser的选项一样，可以用parents来实现

### add_argument() 添加参数

- name or flags - 指定参数的形式，一般写两个一个短参数，一个长参数。保存的变量名为第一个参数的名字
- dest - 设置参数在代码中的变量名  #parser.add_argument(“-q”, dest=”world”)
- nargs - 指定这个参数后面的value有多少个，例如，我们希望使用-n 1 2 3 4，来设置n的值为[1, 2, 3, 4] #parser.add_argument(“-n”, “–num”, nargs=”+”, type=int) # 这里nargs=”+”表示，如果你指定了-n选项，那么-n后面至少要跟一个参数，+表示至少一个,?表示一个或0个,0个或多个 。
- default - 如果命令行没有出现这个选项，那么使用default指定的默认值 #parser.add_argument(“+g”, “++gold”, help=”test test test”,default=”test_gold”)#需要prefix_chars包含”+” 。
- type - 如果希望传进来的参数是指定的类型（例如 float, int or file等可以从字符串转化过来的类型），可以使用 #parser.add_argument(“-x”, type=int) 。
- choices - 设置参数值的范围，如果choices中的类型不是字符串，记得指定type哦 #parser.add_argument(“-y”, choices=[‘a’, ‘b’, ‘d’])
- required - required=True表示必须设置值 #parser.add_argument(“-z”, choices=[‘a’, ‘b’, ‘d’], required=True)
- metavar - 参数的名字，在显示 帮助信息时才用到. # parser.add_argument(“-o”, metavar=”OOOOOO”)
- help - 设置这个选项的帮助信息
- args = parser.parse_args(args) # 如果你没有args参数，那么就使用sys.argv，也就是命令行参数啦。有这个参数，就方便我们调试啊 。# args.world就是-q的值啦
- action 当在命令行中遇到此参数时所采取的基本操作类型。 

>action内置6种动作可以在解析到一个参数时进行触发：
store 保存参数值，可能会先将参数值转换成另一个数据类型。若没有显式指定动作，则默认为该动作。
store_const 保存一个被定义为参数规格一部分的值，而不是一个来自参数解析而来的值。这通常用于实现非布尔值的命令行标记。
*store_ture/store_false 保存相应的布尔值。这两个动作被用于实现布尔开关。
append 将值保存到一个列表中。若参数重复出现，则保存多个值。
append_const 将一个定义在参数规格中的值保存到一个列表中。
version 打印关于程序的版本信息，然后退出



​    


### parser.parse_args() 进行解析

解析后才可以访问其属性值

```
# file-name: help.py
import argparse

def get_parser():
    parser = argparse.ArgumentParser(
        description='help demo')
    parser.add_argument('-arch', required=True, choices=['alexnet', 'vgg'],
        help='the architecture of CNN, at this time we only support alexnet and vgg.')

    return parser


if __name__ == '__main__':
    parser = get_parser()
    args = parser.parse_args()
    print('the arch of CNN is '.format(args.arch))
    
```


## 16 glob

遍历文件夹下的文件，采用正则表达式。

```
for xmlPath in glob.glob('/media/ai1/DATAPART11/LIDC-IDRI' +"/*"):
#解释：遍历指定文件夹下所有文件或文件夹
 
 
for xmlPath in glob.glob(xmlPath + "/*/*"):
#解释：遍历指定文件夹下的所有文件夹里的所有文件，/*/*可以根据文件夹层数自主设定
 
 
img_path = sorted(glob.glob(os.path.join(images, '*.npy')))
#解释：遍历文件夹下所有npy文件
 
 
 
import glob
#获取指定目录下的所有图片
print glob.glob(r"E:/Picture/*/*.jpg")
#获取上级目录的所有.py文件
print glob.glob(r'../*.py') #相对路径
```

## 17 yaml

### yaml配置文件介绍

语法：
- 大小写敏感
- 使用缩进表示层级关系
- 缩进时不允许使用Tab键，只允许使用空格。
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
- #表示注释，从这个字符一直到行尾，都会被解析器忽略，这个和python的注释一样

支持的数据结构：

- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
-数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
-纯量（scalars）：单个的、不可再分的值。字符串、布尔值、整数、浮点数、Null、时间、日期


### 语法案例

```
# yaml
nb1:
    user: admin
    psw: 123456
    lr: 5.0e-4 (不能为5 必须为5.0)
    
# python
"nb1": {
        "user": "admin",
        "psw": "123456,
        }
        
        
#序列
# yaml
- admin1: 123456
- admin2: 111111
- admin3: 222222

# python

[{'admin1': 123456},
{'admin2': 111111}, 
{'admin3': 222222}]

var:~ #~表示none
```

### yaml库

- yaml.load() 读取表示YAML文档的字节字符串
- yaml.dump() 接受一个Python对象并生成一个YAML文档

```
#读取
import yaml
f = open(r'./test.yml')
y = yaml.load(f)
yaml.safe_load(f)
print(y)


#写入
>>> with open('document.yaml', 'w') as f:
...     yaml.dump(emp_info, f) //将emp_info写入f中
... 

```











end
