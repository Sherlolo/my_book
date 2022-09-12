# c++ effective

> 取乎其上，得乎其中，取乎取中，得乎其下，取乎取小，则无所得矣



# 第一章 C++基础概论

## 条款01 视C++为一个语言联邦

- c++高效编程视情况而变化，取决于你使用c++的哪一部分



c++最开始只是c加上一些面向对象特性。

而现在的c++已经是多重范式编程语言，一个同时支持过程形式，面向对象形式，函数形式，泛型形式，元编程形式。

## 条款02 尽量以const、enum、inline替换#define

- 对于单纯常量，最好以const对象或enums替换#defines
- 对于形式函数的宏，最好改用inline替换#defines

### define宏处理

- FLAG从未被编译器看见，它会在预处理器处理后，就进行了替换。所以编译器保持的信息将是1而不是FLAG。

- #define定义的变量，在整个编译过程都有效，除非在某处被#undef。

- enum比较像#define而不像const。

- enum不会被指针或引用使用。

C++ 执行过程：预处理、编译、汇编、链接

```c++
#define FLAG 1
```

define宏处理写函数的写法：

```c++
//以do {} while(0) 的形式来替换函数宏
#define EXPECT(c, ch)       do { assert(*c->json == (ch)); c->json++; } while(0)
```

### enum

enum和#define类似。enum不会被指针或引用所使用。

## 条款3 尽可能使用const

- 将某些东西声明为const能帮助编译器去甄别错误用法
  （作用域对象，函数参数，函数返回类型，成员函数本体）
- 编译器强制实施 bitwise constness(常量性),但我们编写程序时应该使用 “概念上的常量性”
- 当const 和 non-const成员函数有着实质等价的实现时，令 non-const 版本调用const版本可避免代码重复

### const用于指针或迭代器

```c++
const Widget* pw;	//const在左边，表示被指物是一个常量
Widget const* pw;	//const在右边，表示指针本身是常量

const iterator;		//自身是常量
const_iterator;		//迭代器指向物是常量
```

### const成员函数

两个成员函数只是常量性(constness)不同，可以被重载。

例子：

```c++
tb[0] = 'x'; //如果operator返回一个内置类型char，改动函数返回值是不合法的。
```

### 常量性转换

const有两个流行概念：

- bitwise const: 不更改对象内的任何一个bit
- logical const: const成员可以修改它所处理的对象内的某些bits

> mutable实现logical const

```c++
class Block
{
private:
	char* text;
	std::size_t textlength;
	bool flag;
}

std::size_t Block::length() const
{
	if(!flag)
		textlength = std::strlen(Ptext);	//错误 const成员函数不可修改对象成员
}
```

可以在变量前面加入mutable释放掉const约束

```c++
class Block
{
private:
	char* text;
	mutable std::size_t textlength;
	mutable bool flag;
}
```

## 确定对象被使用前已先被初始化

- 为内置型对象进行手工初始化，因为 C++不保证初始化他们
- 构造函数最好使用成员初始值，而不要在构造函数本体内使用赋值操作， 且初始列列出的成员变量，其排序次序应该和他们在 class 中声明次序相同
- 为避免“跨编译单元之初始化次序”问题， 请以 local static 对象替换 non-static 对象
  

### 关于构造函数的初始化

构造函数一个好的写法：使用member initialization list（成员初始化列）替换赋值操作.

```c++
ABC::ABC(const std::string& name, const std::string& address) : theName(name), theAddress(address)
{}
//初始化发生在构造函数之前
//构造函数内部的都不算是初始化而是赋值
```

### 关于static对象的声明

static对象包括：

- 全局static对象：global对象，namespace作用域内的对象
- 局部static对象：函数内的对象。

> 注意：定于于不同编译单元内的non-local static对象的初始化相对次序并无明确定义。

为了解决正确次序的问题，可以使用***Singleton模式***:

- 将每个全局static对象搬到自己的专属函数内。
- 这些函数返回一个引用对象，再由用户来使用。



# 第二章 构造/析构/赋值运算

## 条款05： 了解C++默认调用哪些函数

编译器可以暗自为class 创建default构造函数，copy构造函数，copy assignment 操作符，以及析构函数。

- 默认拷贝赋值运算符/拷贝构造函数在成员含引用类型时不能被生成。

- 当基类将拷贝赋值运算符/拷贝构造函数声明为 private或 delete， 也是不能被生成的。

## 条款06： 若不想使用编译器自动生成的函数，就该明确拒绝

- 将成员函数声明为private而且故意不实现它
- 将成员函数声明为delete(c++11)

## 条款07： 为多态基类声明 virtual 析构函数

- polymorphic(带多态性质) base classes 应该声明一个virtual析构函数， 如果 class 带有任何 virtual 函数，他就应该拥有一个 virtual 析构函数

- Class 的设计目的如果不是当作 base classes 使用，就不应该声明 virtual 析构函数。

> 关于virtual函数实现的细节
>
> virtual函数的实现，会使对象携带某些信息，通常是一个vtbl(virtual table)：在运行期决定哪个函数被运行。
>
> 如果含有虚函数，其对象的体积会大大增大
>
> 并且不可能把它传递到其他语言写的函数中去。

## 条款08： 别让异常带进析构函数

c++不禁止析构函数吐出异常，但不推荐这样使用。

- 析构函数绝不要吐出异常， 如果一个被析构函数调用的函数可能抛出异常，析构函数应该捕捉任何异常， 然后吞下他们（不传播）或结束程序。

- 如果客户端需要对某个操作函数运行期间抛出的异常做出反应，那么 class 应该提供一个 普通函数（而非在析构函数种）执行该操作
- 可以对析构函数noexcept，使之不抛出异常

使用案例：

```c++
vector<widget> v;
//在v被销毁时，如果其第一个和第二个销毁时，发生异常。
//在两个异常同时存在的情况下，会导致不明确的行为
```

## 条款09： 绝不在构造和析构过程中调用 virtual 函数

- 在base class 构造和析构期间不要调用 virtual 函数，因为这类调用从不下降至 derived class
  (比起当前执行构造函数和析构函数那层)

> 原因：
>
> 在derived class对象的base class构造期间，对象的类型时base class而不是derived class.

## 条款10： 令 operator= 返回一个 reference to *this

- 令赋值(assignment)操作符返回一个引用

这份协议可以说是为了实现***连锁赋值***而创造的协议.

## 条款11： 在 operator= 中处理 “自我赋值”

- 确保当对象自我赋值时 operator= 有良好的行为（鲁棒性，通用性更强），包括：
  - 比较来源对象和目标对象的地址
  - 精心周到的语句顺序(先赋值，再释放)
  - copy and swap
- 确保任何函数如果操作一个以上的对象，且多个对象都是同一个对象时，其行为仍然正确。

关于自我赋值的解决方法：

```c++
//比较地址
Widget& Widget::operator=(const Widget& rhs)
{
	if(this == &rhs)
        	return *this;
    delete pb;
    pb = new Bitmap(*rhs.pb);	//new异常，导致pb没有赋值，此时pb指向一块被删除的空间
    return *this;
}

//正确的语句顺序
Widget& Widget::operator=(const Widget& rhs)
{
	if(this == &rhs)
        	return *this;
    Bitmap* pOrig = pb;
    pb = new Bitmap(*rhs.pb);
    delete pOrig;
    return *this;
}

//copy and swap
Widget& Widget::operator=(const Widget& rhs)
{
	Widget temp(rhs);
    swap(temp);
    return *this;
}
```

## 条款12：复制对象时勿忘其每一个成分

- copying 函数应该确保复制 ”对象内的所有成员变量及所有 base class 成分“
- 不要尝试以某个 copying 函数实现另一个 copying函数，应该将共同机能放进第三个函数中
  并由两个 copying 函数共同调用.

这里如果没有复制新添加的变量，编译器也是不会进行提醒的，因为你已经拒绝编译器进行为你生成 copy 函数.

拷贝赋值函数和拷贝构造函数不应该相互调用。

# 第三章 资源管理

## 条款13： 以对象管理资源

- 为了防止资源泄漏，请使用 RAII对象， 他们在构造函数中获得资源并在析构函数中释放资源
- 两个常被使用的 RAII 对象 classes 分别是 std::shared_ptr 和 std::unique_ptr(std::auto_ptr被std::unique_ptr代替)
  shared_ptr 是一个 RCSP(reference count smart pointer)

即多使用自动指针管理资源，而不是自己构建资源的释放。

- 获得资源后立刻放进管理对象内
- 管理对象运用析构函数确保资源被释放

## 条款14： 在资源管理类中小心 copying 行为

- 复制 RAII 对象必须一并复制它所管理的资源，资源的copying行为决定RAII对象的copying行为
- 普遍而常见的 RAII class copying行为是： 抑制copying，实施引用计数

对资源管理类的拷贝注意如下:

- 禁止拷贝。类似于互斥锁的Mutex是禁止复制的
- 对底层资源使用"引用计数器"。shared_ptr采用的就是这种决策来管理资源的
- 复制底部资源。因为原始的资源需要被释放，所以复制的过程是一个深拷贝。
- 转移资源的拥有权。auto_ptr的拷贝过程即采用的这种决策方法，或者get()方法返回普通指针。

## 条款15： 在资源管理类中提供对原始资源的访问

- APIs 往往要求访问原始资源，所以每个 RAII class 应该提供一个
  ”取得其所管理之资源“ 的办法
- 对原始资源的访问可能经由显示转换或隐式转换。一般而言显示转换比较安全，
  但隐式转换对客户比较方便。

提供资源的访问有两种方式，即显式转换和隐式转换：

- 显式转换。使用一个方法(比如get())返回普通指针来进行调用
- 隐式转换。内部定义转换函数，可以直接向普通指针传参。
- 显式转换看似和封装发生了矛盾。但是自动指针的设计是为了让资源能正确的使用
- 从"让接口容易被正确使用，不易被误用"的思想来讲，应多使用显式转换

## 条款16： 成对使用 new 和 delete 时要采取相同形式

- new 对应 delete， new[] 对应 delete[]
- 尽量少使用数组，多使用 vector string 标准库

## 条款17： 以独立语句将 newed 对象置入智能指针

- 以独立语句将newed对象存储于智能指针中。如果不这样做，一旦异常被抛出，有可能导致难以察觉的资源泄漏。

# 第四章 设计和声明

## 条款18： 让接口容易被正确使用， 不易被误用

- 好的接口很容易被正常使用，不容易被误用
- “促进正确使用”的办法包括接口的一致性， 以及与内置类型的行为兼容
- “阻止误用” 的办法包括建立新类型， 限制类型上的操作，束缚对象值， 以及消除客户的资源管理责任
- shared_ptr 支持定制型删除器，可防范在不同DLL间出现的引用计数的问题，可被用来自动解除互斥锁等资源问题
- 好的接口可以防止无效代码通过编译

接口是客户与你的代码互动的手段，在设计时应考虑客户会做出什么样的错误。

避免无端与内置类型不兼容，提供行为一致的接口。

> cross-dll-problem
>
> 对象在动态链接库(dll)中被new创建，却在另一个动态链接库被delete释放。会带来程序崩溃问题。
>
> 解决方法使用shared_ptr来管理对象。

## 条款19： 设计 class 犹如设计 type

设计高效规范的class，应注意如下问题：

1. 新的type的对象应该如何被创建和销毁？ （构造函数析构函数）
2. 对象的初始化和对象的复制该有什么样的差别？ （构造函数与拷贝赋值运算符，初始化和赋值的区别）
3. 新type的对象如果被passed by value（以值传递），意味着什么？ （copy构造函数）
4. 什么是新type的 “合法值”？ （维护约束条件，要在构造，赋值，setter函数进行错误检查）
5. 你的新 type 需要配合某个继承图系？ （virtual 与 non-virtual的影响， 特别是析构函数 virtual）
6. 你的新 type 需要什么类型的转换？ （explict 与 non-explict，以及隐式转换运算符定义）
7. 什么样的操作符和函数对该新type而言是合理的？ (声明哪些函数， memeber函数还是否)
8. 什么样的标准函数应该被驳回？ （ 编译器自动生成的那些声明其为 delete）
9. 谁该取用新的 type 的成员？ （存取函数进行约束）
10. 什么是新的 type 的“未声明接口”？ （undeclared interface）
11. 你的新 type 有多么一般化？ （template 或 一整个types家族）
12. 你真的需要一个新type吗？ （如果只是为了扩充功能而进行派生，倒不如直接定义一个 non-member函数）

## 条款20： 宁以 pass-by-reference-to-const 替换 pass-by-value

- 尽量以 pass by reference, 即高效也支持多态，避免切割问题
- 内置类型以及 STL 迭代器和函数对象，使用 pass by value 比较适当，（属于c语言块的内容以c语言方式进行处理）

使用形参的优势：

- 使用引用类型能提升效率问题
- 使用引用进行传参可以实现多态且避免对象切割问题，引用底层的实现也是靠指针。

```c++
//对象切割问题
baseclass A;
deriveclass B;
void fun(baseclass  A);

fun(B);	//在函数内部B将转换为baseclass类型，使之无法实现多态
```

## 条款21： 必须返回对象时，别妄想返回其 reference

- 绝对不要返回 pointer 或 reference 指向的一个 local stack 对象，或者返回引用指向一个
  heap-allocated 对象，
- 或返回 pointer 或 reference 指向一个 local static对象。而有可能同时需要多个这样的对象

```c++
const Rational& operator*(const Rational& lhs, const Rational& rhs)
{
	Rational* result = new Rational(lhs.n * rhs.n);
    return *result;
}
Rational w, x, y, z;
w = x*y*z;	//等价于operator*(operator*(x, y), z)

//问题：
//两次使用new，应调用两次delete，但operator*(x, y)返回的对象丢失，造成资源泄漏
```

## 条款22： 将成员变量声明为 private

- 切记将成员变量设置为 private，这可赋予客户访问数据的一致性，可细微划分访问控制，允许约束条件保证，并提供 class 作者以充分的实现弹性。

- 仅存在两种访问权限： private（提供封装）和 其他（不提供封装），protected 并不比 public 更具封装性。

## 条款23： 宁以non-member，non-friend 替换 member 函数

- 宁可拿 non-member non-friend 函数替换member函数，这样做可能增加封装性，包裹弹性
  和机能扩充性。

```c++
class webBrowser{
public:
	void clearCache();
	void clearHistory();
}

void clearBrowser(webBrowser& wb)
{
	wb.clearCache();
    wb.clearHistory();
}
```

面向对象守则要求数据应该尽可能被封装，因此很多方面non-member比member函数好。

- 封装的好处：它使我们能够改变事物而只影响有限客户。
- 越多函数访问，数据的封装性就越低。
- 函数成为class的non-member，并不意味着它不可以是另一个class 的member
- 较好的解决方法是：将所有便利函数放在多个头文件但隶属于同一个命名空间，客户可以轻松扩展这些便利函数

## 条款24： 若所有参数皆需类型转换，请为此采用 non-member 函数

- 若重载运算符函数的操作数皆需类型转换，就声明其为非成员函数

```c++
class Rational{
public:
    const Rational operator*(const Rational& rhs) const;
};

Rational result, oneHalf;
result = oneHalf * 2;	//正确
result = 2 * oneHalf;	//错误
```

如上：oneHalf*2中的2被隐式转换为Rational。但2\* oneHalf中的2不能进行转换。

原因：只有当参数被利于参数列内，这个参数才是隐式类型转换的合格参与者。参数列：所属对象的参数，不能说this

## 条款25： 考虑写出一个不抛异常的 swap 函数

- 提供一个 public swap 成员函数，让它高效地置换你的类型的两个对象值，这个函数不能抛出异常
- 在你的 class 或 template 所在的命名空间内提供一个 non-member swap，并令它调用上述 swap 成员函数
- 如果你正编写一个 class， 为你的class 特化 std::swap ，并令它调用你的 swap 成员函数
- 最后调用 swap 时，确保包含一个 using 声明符，以便让 std::swap 在你的函数曝光
  然后不加任何 namespace 修饰符， 赤裸裸地调用 swap。
- 注意：不能抛出异常，因为swap是帮助 class 提供异常安全性的保障， 基于的条件就是 swap不能抛出异常

### swap的实现

std中默认的swap实现：

```c++
namespace std{
	template<typename T>
    void swap(T& a, T& b){
		T temp(a);
        a = b;
        b = temp;
    }
}
```

上述的实现涉及三个对象的复制，效率并不高。

可以使用"pimpl手法"(pointer to implemention)

```c++
//使用具体类型重载模板函数
template<>
void swap<Widget>(Widget& a, Widget& b){
    swap(a.pImpl, b.pImpl);
}

//使用时
void fun(){
    using std::swap;
    swap(obj1, obj2);
}
```

> - 注意我们不能改变std命名空间内的任何东西，但可以为标准templates制造特化版本
> - 使用先声明标准库的swap。这样做的目的:
>   - 优先查找专属的swap
>   - 没有找到专属swap，使用默认的swap。

# 第五章 实现

## 条款26： 尽可能延后变量定义式的出现时间

- 不仅只是延后变量的定义，直到非带使用该变量的前一刻为止，甚至应该尝试延后这份定义直到能够给它初值实参为止。
- 不仅能够避免构造（析构）非必要对象，还能避免无意义的default的构造行为。

## 条款27： 尽量少做转型动作

- 如果可以，请避免转型，特别是注重效率的代码中避免 dynamic_casts，如果有个
  设计需要转型动作，请试着发展无须转型的替代设计
- 如果转型是必要的，试着将它隐藏于某个函数背后， 客户随后可以调用该函数， 而不需要
  将转型放在他们的代码中， othertype ObjectToOther(object &obj)
- 宁可使用C++ style(新式)转型， 不要使用旧式转型， 前者很容易辨识出来，而且也有比较分门别类的职掌

```c++
//对派生类进行转换
class Window{
    //...;
}

class SpecialWindow : public Window{
public:
    virtual void OnResize(){
        static_cast<Window>(*this).OnResize();	//错误的使用，这里使用的时副本形式，不会改变到this
        Window::OnResize();	//正确的使用，会改变到基类
    }
}
```

## 条款28： 避免返回 handles 指向对象内部成分

避免返回 handles(包括reference， 指针，迭代器)指向对象内部。遵守这个准则可以增加封装性，帮助 const 成员函数像一个 const，减少空悬的发生（**内部的临时对象被指向**）

```c++
class Rectangle{
public:
	Point& upperLeft() const {return pData->ulhc;}
	Point& lowerRight()	const {return pData->lrhc;}
}

const Rectangle rec(coord1, coord2);
rec.upperleft().setX(x);	//通过返回内部对象的引用，修改对象的值

//修改如下
class Rectangle{
public:
	const Point& upperLeft() const {return pData->ulhc;}
	const Point& lowerRight()	const {return pData->lrhc;}
}
```

## 条款29： 为 “异常安全” 而努力是值得的

- 异常安全函数(Exception-safe function)即使发生异常也不会泄漏资源或允许任何数据结构败坏。这样的函数区分为三种可能的保证：基本型、强烈型、不抛异常型。
- ”强烈保证”往往能够以copy-and-swap实现出来，但”强烈保证”并非对所有函数都可实现或具备现实意义。
- 函数提供的”异常安全保证”通常最高只等于其所调用之各个函数的”异常安全保证”中的最弱者。
  



异常抛出时，带有异常安全性的函数会

1.不泄露任何资源 2.不允许数据败坏

### 异常安全性提供以下三个保证之一：

- 基本承诺：如果异常被抛出，程序内的任何事物仍然保持在有效状态下。
  没有任何对象或数据结构因此而败坏，所有对象都处于一种内部前后一致的状态

- 强烈保证：如果异常被抛出， 程序状态不改变。
  调用可能抛出异常的操作，如果调用失败应该恢复到调用之前的状态，调用成功就是完成成功

- 不抛出异常保证： 承诺绝不抛出异常， 因为他们总是能完成它们原先承诺的功能。
  （作用于内置类型） nothrow

## 条款30： 透彻了解 inlining 的里里外外

- inline 在C++程序中是编译期行为，这样可能会增加你的目标码大小
- inline函数和templates两者通常被定义在头文件内。大多数都是在编译过程中进行inlining

- 过度热衷inline会造成程序体积体大，即使有虚拟内存，inline 造成的代码膨胀亦会导致额外的换页行为，降低指令高速缓存装置的击中率，降低效率。

- virtual 的调用会使得 inlining 落空 （运行期确定的多态行为，会使得inlining落空）

- 大部分调试器面对 inline 函数都束手无策（inline函数不能设置断点)，是否成为inline函数最终由编译器决定。
- 如果要去某个inline函数的地址，将为此函数生成一个实体。

- 将大多数 inlining 限制在小型，被频繁调用的函数身上，可以使得日后的调试和二进制升级更容易，

  也可使得潜在的代码膨胀问题最小化，使程序的速度提升机会最大化



> inline函数无法随着程序库的升级而升级,即如果inline函数被改变,所有用到该函数的库都会改变.
>
> 而对于一般函数,客户端只需要重新连接就好.



## 条款31： 将文件间的编译依存关系降至最低

- 支持“编译依存性最小化”的一般构想是： 相依于声明式， 不要相依定义式
  基于此构想两个手段是 Handle classes（将自身的实现作为另一个类，且将该类的指针作为自己的实现） 和 Interface classes（以抽象类作为接口）
- 程序库头文件应该以 “完全且仅有声明式” 的形式存在。
  

具体而言：

- 编译依存性最小化的性质: 声明的依存性替换定义的依存性
- 如果使用object references或object pointers可以完成任务，就不要使用objects
- 为声明式和定义式提供两个不同的文件



# 第六章 继承和面向对象设计

## 条款32： 确定你的 public 继承素模出 is-a 关系

- public 继承意味着 is-a，适用于 base class 的事情也一定适用于 derived class身上
  未遵循is-a的代码即使编译通过，但不保证程序的行为是正确的



## 条款33：避免遮掩继承而来的名称

- derived class 内的名称会遮盖 base classes 内的名称， 在public 继承下从来没有人希望如此
- 为了让遮盖的名称再见天日，可使用using声明式子，或转交函数 类名::function



案例：

```c++
class base{
	void mf3();
    void mf3(int);
};

class derive : base{
  voif mf3();	//会将基类的两个mf3遮掩掉  
};
```

解决方法：

```c++
//using声明式

class derived : base
{
	using base::mf3;
};

//转交函数

class derived : base
{
	virtual void mf1()
    {
        base::mf1();	//暗自成为inline
    }
};

```

## 条款34： 区分接口继承和实现继承

- pure virtual函数的目的是为了让 derived class 只继承函数接口
- impure virtual 函数的目的是让 derived class 继承该函数的接口和缺省实现
- non-virtual函数的目的是为了令derived class 继承函数的接口及一份强制性实现（不变性，不应该被重新定义）
  

##  条款35： 考虑 virtual 函数以外的其他选择（std::function?）

- virtual 的替换手法八廓 NVI 手法以及 Strategy 设计模式的多种形式

  NVI 手法自身是一个特殊形式的 Template Method 设计模式

- 将机能从成员函数移到 class 外部函数，带来的一个缺点是，非成员函数无法访问
  class 的 non-public 成员。

- std::function 对象的行为就像一般函数指针，这样的对象可接纳 “与给定之目标签名式” 兼容
  的所有可用调用物。



> 为什么考虑virtual以外的函数

1. virtual 函数带来的虚函数表以及虚表指针的负担
2. 其灵活性十分差，继承重写虚函数后不具有可变与可增内容灵活度



### 使用 NVI(Non-Virtual Interface) 手法实现 Template Method 模式（主张 virtual应该总是private）

私有impure virtual 使得派生类重新定义， member function 调用其 impure virtual 实现（且在调用前后都可以做额外的事情）

NVI手法运行derived class重新定义virtual函数，从而赋予它们“如何实现机能”的能力

base class保留"函数何时被调用"的权利

```c++
class GameNvi{
public:
    int healthValue() const{
        int health_val = doHealthValue();
    }
private:
    virtual int doHealthValue() const{
        //计算健康值
    }
}
```



### 以策略模式替换 virtual， 以基于对象的思路（std::bind + std::function）替换virtual+多态

- 将virtual函数替换为函数指针成员变量
- 将virtual函数替换为tr1::function，可以允许任何可调用物搭配一个签名式
- 将virtual函数替换为另一个体系内的virtual函数。

## 条款36： 绝不重新定义继承而来的 non-virtual 函数

绝不重新定义继承而来的 non-virtual 函数

## 条款37： 绝不重新定义继承而来的缺省参数值

- 绝对不要重新定义一个继承而来的缺省参数值，因为缺省参数都是静态绑定，
  而virtual函数—你唯一应该覆写的东西-却是动态绑定。

- 静态绑定并不会从base继承默认参数，动态绑定会从base继承默认参数

```c++
class shape{
public:
    virtual void draw(ShapeColor color = Red) const;
}

class circle : public shape{
public:
    virtual void draw(ShapeColor color = Green) const;
}

Shape* pc = new circle;	//静态类型为Shape* 动态类型为circle
circle.draw();	//调用时会使用base的默认参数 即
```

