# Numpy、Panda和matplotlib

标签（空格分隔）： study

[TOC]

---

#Numpy

---

Numpy的ndarray对象再内存中是连续分配的，并且其运算速率十分的快

## ndarray基本使用

    np.sum(dz2, axis=1, keepdims=True) 
    #keepdims 防止矩阵变量变成(2,) 这样奇怪的类型


### array属性

```
import numpy as np #为了方便使用numpy 采用np简写
array = np.array([[1,2,3],[2,3,4]])  #列表转化为矩阵

print('number of dim:',array.ndim)  # 维度
# number of dim: 2

print('shape :',array.shape)    # 行数和列数
# shape : (2, 3)

print('size:',array.size)   # 元素个数
# size: 6

array.dtype #元素类型
# dtype('int32')

array.itemsize #每个元素占有的字节大小
# 4
array.data #数字元素的缓冲区
# <memory at 0x0000020DF2865048>

```

### 数组创建

```
#创建矩阵
arange(a,b,c) 参数分别表示开始值，结束值，步长
linspace(a,b,c) 参数分别表示开始值，结束值，元素数量

#高斯分布
np.random.randn(shape)：生成对应形状（shape）的高斯分布
np.random.normal(loc, scale, size)：生成均值为loc，标准差为scale，形状（shape）为size的高斯分布

#均匀分布
np.random.rand(shape)：生成对应形状（shape）的均匀分布
np.random.uniform(low, high, size)：生成一个从[low, high)中随即采样的，样本数量为size的均匀分布

#特殊数组
zeros ones empty 全零、全1、空数组

```



### 索引和切片

```
col0_numpy = arr_slice[:,0] 选取第一列
col0_1 = arr_slice[:,0:2] #选取前两列
arr_sub = arr_slice[0:2,0:2] #选取前两行和前两列

arr.item()  #返回值

```

### 算数运算

```
自运算
A.sum() A.min() A.max() A.mean()

np.dot(A,B) #矩阵乘法

A+B #对应相加
A-B #对应相减
A*B #对应维度对应相乘
A**2 #平方运算
```

### 逻辑运算

```
arr > a : 返回arr中大于a的一个布尔值数组
arr[arr>a] : 返回arr中大于a的数据构成的一维数组
np.all(): 括号内全为真则返回真，有一个为假则返回false
np.any() : 括号内全为假则返回假，有一个为真则返回真
np.where(): 三元预算符, 判断同时赋值 
np.where(arr>0, 1, 0) arr>0则赋1 否则赋0

```

    b = a.copy() #是拷贝一个备份 对b的修改不会影响到a

### 合并和分割

```
#合并
np.hstack((a,b))：按行合并，要求a和b的行 数 相 同 \color{red}{行数相同}行数相同
np.vstack((a,b))：按列合并，要求a和b的列 数 相 同 \color{red}{列数相同}列数相同
np.c_[a,b]：用法如同np.hstack((a,b))
np.r_[a,b]：用法如同np.vstack((a,b))
np.concatenate((a,b), axis = 1)：按行合并，要求a和b的行数相同
np.concatenate((a,b), axis = 0)：按列合并，要求a和b的列数相同

#分割
split(ary, indices_or_sections, axis=0)
把一个数组从左到右按顺序切分

参数：
ary: 要切分的数组
indices_or_sections: 如果是一个整数，就用该数平均切分，如果是一个数组，为沿轴切分的位置（左开右闭）
axis： 沿着哪个维度进行切向，默认为0，横向切分。为1时，纵向切分

np.array_split(arr, n)：类似上面的用法，但是可以不均等划分
```

### 降维

```
ravel()：返回一维数组，但是改变返回的一维数组内容后，原数组的值也会相应改变
flatten()：返回一维数组，改变返回的数组不影响原数组
```

##  矩阵运算

### 矩阵的创建

```
A = np.matrix('1.0 2.0;3.0 4.0')

```

### 矩阵的运算

```
A.T #转置

np.dot() #矩阵乘积 向量的内积
np.vdot() #两个向量的点积 对应相乘再求其和
numpy.inner() #此函数返回一维数组的向量内积。 对于更高的维度，它返回最后一个轴上的和的乘积。

numpy.matmul()
#函数返回两个数组的矩阵乘积。 虽然它返回二维数组的正常乘积，但如果任一参数的维数大于2，则将其视为存在于最后两个索引的矩阵的栈，并进行相应广播。
另一方面，如果任一参数是一维数组，则通过在其维度上附加 1 来将其提升为矩阵，并在乘法之后被去除

numpy.linalg.det() #计算输入矩阵的行列式

numpy.linalg.solve() #该函数给出了矩阵形式的线性方程的解。
```

## random

### np.random.beta() beta分布，狄利克雷分布

    np.random.beta(a,b) #a代表正例个数 b代表负例个数
    
beta分布会利用先验信息产生分布。



#matplot

## 快捷使用

import matplotlib.pyplot as plt
import numpy as np

## figure对象和axes轴

### figure对象类似与画板，先创建画板才能画图

```
fig = plt.figure() #创建figure对象
ax1 = fig.add_subplot(221) # 添加图例 221代表生产2*2的矩阵图例 1代表第一个位置
ax2 = fig.add_subplot(222)
ax3 = fig.add_subplot(223)
ax1.set(xlim=[0.5, 4.5], ylim=[-2, 8], title = 'An Example', ylabel = 'Y- axis', xlabel='X - axis') #设置轴空间
plt.show()

#或者采用如下形式 先划分好画板和矩阵
fig, axes = plt.subplots(2,2)
plt.subplot(221)
sns.scatterplot()

```

设置axes轴 对于ax1.set的参数解释

- xlim,ylim 设置x轴和y轴的范围
- xlabel, ylabel 设置x和y轴的名字

### 批量生产figure和axes
    fig, axes = plot.subplots(nrows=2,ncols=2)
    axes[0,0].set(title='Upper Left')
    
## 画图plot

### plot的基本使用
    
    plt.plot(x,y,format_string,**kwargs)
    
    ax2.plot(x, y_sin, color='red','go--', linewidth=2, markersize=12)
    
    可以利用plot设置画图中的颜色，大小，样式
    
>format_string:

颜色:
- 'b' 'g' 'r' : 蓝 绿 红
- 'c' 青绿色
- 'y' 黄色
- 'k' 黑色
- 'w' 白色

风格：

- ‘-‘	实线
- ‘–’	破折线
- ‘-.’	点划线
- ‘:’	虚线
- ’ ’ ’ ‘	无线条

标记：

- ‘.’	点标记	
- ‘,’	像素标记
- ‘o’	实心圈标记	
- ‘v’	倒三角标记	
- ‘^’	上三角标记	
- ‘>’	右三角标记	
- ‘<’	左三角标记	
- ‘h’	竖六边形标记		
- ‘H’	横六边形标记		
- ‘+’	十字标记		
- ‘x’	x标记		
- ‘D’	菱形标记		
- ‘d’	瘦菱形标记		


>参数

- color:控制颜色，color=’green’
- linestyle:线条风格，linestyle=’dashed’
- marker:标记风格，marker = ‘o’
- markerfacecolor:标记颜色，markerfacecolor =‘blue’
- markersize:标记尺寸，markersize = ‘20’


    
### plot结合字典类型

```
data_obj = {'x': x, 'y1':2*x+1, 'y2':3*x+1.2, 'mean': 0.5*x*np.cos(2*x)+2.5*x+1.1}
#填充两条线之间的颜色
ax.fill_between('x', 'y1', 'y2', color='yellow', data=data_obj)
#plot
ax.plot('x', 'mean', color='black', data=data_obj)
```

在引进data后，输入的数据将自动在data中寻找

## 散点图scatter

```
x = np.arange(10)
y = np.random.randn(10)
plt.scatter(x, y, color = 'red', marker='+')
plt.show()
```
参数的调用和plot一致

### 泡泡图

```
np.random.seed(19680801)

N = 50
x = np.random.rand(N)
y = np.random.rand(N)
colors = np.random.rand(N)
area = (30 * np.random.rand(N))**2  # 相当于z值以面积的形式表现出来了

plt.scatter(x, y, s=area, c=colors, alpha=0.5)
plt.show()
```

## 直方图hist bar

```
#直方图
np.random.seed(1)
x = np.random.randn(1000, 3)
n_bins = 10

fig, axes = plt.subplots(2,2)
ax0, ax1, ax2, ax3 = axes.flatten()

colors = ['red','tan','lime']
#hist 绘制直方图
ax0.hist(x, n_bins, density=True, histtype='bar', color=colors, label=colors)
```

density控制Y轴是概率还是数量，与返回的第一个的变量对应。
histtype控制着直方图的样式，默认是 ‘bar’，对于多个条形时就相邻的方式呈现如子图1， ‘barstacked’ 就是叠在一起

## 饼状图pie

```
labels = 'Frogs', 'Hogs', 'Dogs', 'Logs'
sizes = [15, 30, 45, 10]
explode = (0, 0.5, 0, 0)  # 表示突出某个值

fig1, (ax1, ax2) = plt.subplots(2)

ax1.pie(sizes, labels=labels, autopct='%1.1f%%', shadow=True)
ax1.axis('equal')

ax2.pie(sizes, autopct='%1.2f%%', shadow=True, startangle=90, explode=explode,
    pctdistance=1.12) #pctdistance表示偏离圆心的距离
ax2.axis('equal')
ax2.legend(labels=labels, loc='upper right')
plt.show()
```



## 图例和标注

### 图例

```
fig, ax = plt.subplots()
ax.plot([1, 2, 3, 4], [10, 20, 25, 30], label='Philadelphia')
ax.plot([1, 2, 3, 4], [30, 23, 13, 4], label='Boston')
ax.scatter([1, 2, 3, 4], [20, 10, 30, 15], label='Point')

#设置纵坐标轴的名字
ax.set(ylabel='Temperature (deg C)', xlabel='Time', title='A tale of two cities')

ax.legend(loc='upper right') #‘lower left’ ‘upper right’
plt.show()
```

通过在plot添加label，再利用legend显示出来


设置坐标轴
```
#设置坐标轴的范围
plt.xlim(-1,2) #设置横轴的方位
plt.xlabel("I am x")

plt.xticks(new_ticks)
plt.yticks([-2, -1.8, -1, 1.22, 3],['relly bad', 'bad', 'normal', 'good', 'very good'])
plt.show()
```

### 标注

```
plt.plot([x0, x0],[0, y0], 'k--', linewidth=2.5)
# 利用虚线画线
plt.scatter([x0,], [y0,], s=50,color='b')

#添加注释
# xy代表选取的点 xycoords代表基于数据的值选取位置 xytext和textcoords表示对于标注位置的描述和xy的偏差值
plt.annotate(r'$2x+1=%s$' % y0, xy=(x0, y0), xycoords='data', xytext=(+30, -30),
             textcoords='offset points', fontsize=16,
             arrowprops=dict(arrowstyle='->', connectionstyle="arc3,rad=.2"))

添加注释 text

# 前面表示的位置
plt.text(-3.7, 3, r'$This\ is\ the\ some\ text. \mu\ \sigma_i\ \alpha_t$',
         fontdict={'size': 16, 'color': 'r'})
```

# Pandas

    Pandas是基于NumPy 的一种工具，该工具是为解决数据分析任务而创建的。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。pandas提供了大量能使我们快速便捷地处理数据的函数和方法。你很快就会发现，它是使Python成为强大而高效的数据分析环境的重要因素之一。

## 日常使用 

```
c.loc[:,'pressure1'] = b.loc[:,'pressure'] #错误 会出现none
c.loc[:,'pressure1'] = b.loc[:,'pressure'].values
```


## Pandas基础

### 文件读取

主要介绍csv、excel、txt、xlsx文件

    df_csv = pd.read_csv(path, **argars)
    df_txt = pd.read_tabel(path, **argars)
    df_excel = pd.read_excel(path, **argars)

参数：

- header:None 表示第一行不作为列名
- index_col 表示把某一列或几列作为索引
- usecols 表示读取列的集合，默认读取所有的列
- parse_dates 表示需要转化为时间的列
- nrows 表示读取的数据行数
- sep 在读取txt文件时 表示分隔符 采用的时正则化表达式


注：以上操作皆可以换成行或列操作

### 数据写入

    df.to_csv(path,index=False) #txt csv
    df.to_excel(path,index=False)
    df.to_markdown(path,index=False)
    df.to_latex(path,index=False)

参数：

- index 表示是否保存索引
- sep 以sep进行分割

### 数据结构

主要有一维的series和二维的DataFrame组成

>series

    s = pd.Series(data = [100, 'a', {'dic1':5}],
                 index = pd.Index(['id1', 20, 'third'], name='my_idx'),
                 dtype = 'object',
                 name = 'my_name')
                 
注：object表示一种混合类型

>DataFrame

```
#索引对应列表的形式
df = pd.DataFrame(data = data,
               index = ['row_%d'%i for i in range(3)],
               columns=['col_0', 'col_1', 'col_2'])
               
#字典的形式构造
df = pd.DataFrame(data = {'col_0': [1,2,3], 'col_1':list('abc'),
                           'col_2': [1.2, 2.2, 3.2]},
                            index = ['row_%d'%i for i in range(3)])

```

注：index 表示行属性 colums表示列属性 dataframe.T表示转置


>映射关系和属性

```
df['col_0']

df[['col_0','col_1']] 

df[['col_0':'col_1']]  #错误 没有切片操作

In [37]: df.values
Out[37]: 
array([[1, 'a', 1.2],
       [2, 'b', 2.2],
       [3, 'c', 3.2]], dtype=object)

In [38]: df.index
Out[38]: Index(['row_0', 'row_1', 'row_2'], dtype='object')

In [39]: df.columns
Out[39]: Index(['col_0', 'col_1', 'col_2'], dtype='object')

In [40]: df.dtypes # 返回的是值为相应列数据类型的Series
Out[40]: 
col_0      int64
col_1     object
col_2    float64
dtype: object

In [41]: df.shape
Out[41]: (3, 3)
```


### 汇中函数
    df.head(2)
head, tail 函数分别表示返回表或者序列的前 n 行和后 n 行，其中 n 默认为5：

    df.info()
    df.describe() #对float、int求平均、方差等
info, describe 分别返回表的信息概况和表中 数值列对应的主要统计量 

### 特征统计函数

>注意针对的是数值对象

在 Series 和 DataFrame 上定义了许多统计函数，最常见的是 sum, mean, median, var, std, max, min 

- idmax 返回最大值索引

### 唯一值函数

对序列使用 unique 和 nunique 可以分别得到其唯一值组成的列表和唯一值的个数：
    
```
In [57]: df['School'].unique()
Out[57]: 
array(['Shanghai Jiao Tong University', 'Peking University',
       'Fudan University', 'Tsinghua University'], dtype=object)

In [58]: df['School'].nunique()
Out[58]: 4
```

value_counts 可以得到唯一值和其对应出现的频数：

### 替换函数

一般而言，替换操作是针对某一个列进行的，因此下面的例子都以 Series 举例。 pandas 中的替换函数可以归纳为三类：映射替换、逻辑替换、数值替换。其中映射替换包含 replace 方法、第八章中的 str.replace 方法以及第九章中的 cat.codes 方法，此处介绍 replace 的用法。

>映射替换

```
df['Gender'].replace({'Female':0, 'Male':1}).head()
df['Gender'].replace(['Female', 'Male'], [0, 1]).head()
```

>逻辑替换 针对数值型

```
s.where(s<0, 100) #条件false时替换
s.mask(s<0, -50) #条件True时替换
```

>数值替换

数值替换包含了 round, abs, clip 方法，它们分别表示按照给定精度四舍五入、取绝对值和截断：


### 排序函数

排序共有两种方式，其一为值排序，其二为索引排序，对应的函数是 sort_values 和 sort_index 。

```
df_demo.sort_values('Height')
df_demo.sort_values('Height', ascending=False) #ascending=True 为升序： 
df_demo.sort_values(['Weight','Height'],ascending=[True,False]) #表示体重相同的情况比较身高
```

### apply方法

得益于传入自定义函数的处理， apply 的自由度很高，但这是以性能为代价的。一般而言，使用 pandas 的内置函数处理和 apply 来处理同一个任务，其速度会相差较多，因此只有在确实存在自定义需求的情境下才考虑使用 apply 。


```
df_demo = df[['Height', 'Weight']]

def my_mean(x):
    res = x.mean()
    return res
    
df_demo.apply(my_mean)

```

## 索引

一种是基于元素的 loc 索引器，另一种是基于位置的 iloc 索引器。  

### loc按值索引器

注意

- 在使用行索引时 一定要设置索引 set_index 如果没有设置可用整数索引访问
- 如果要使用切片操作，其属性必须为唯一值
- 不能使用start: end: step的形式
-

```
df_demo.loc['Qiang Sun'] #单个元素
df_demo.loc[['Qiang Sun','Quan Zhao'], ['School','Gender']] #去除对应元素
df_demo.loc['Gaojuan You':'Gaoqiang Qian', 'School':'Gender'] #切片 值必须唯一

df_demo.loc[df_demo.Weight>70].head() #按条件取值

df_demo.loc[condition] #按函数取值
```

### 按照位置iloc索引器

切片和列表一样
使用布尔列表的时候要特别注意，不能传入 Series 而必须传入序列的 values ，否则会报错。因此，在使用布尔筛选的时候还是应当优先考虑 loc 的方式。

```
df_demo.iloc[(df_demo.Weight>80).values].head()
df_demo.iloc[[0, 1], [0, 1]] # 前两行前两列
```

## query方法 查询表达式对应的值

```
 df.query('((School == "Fudan University")&'
   ....:          ' (Grade == "Senior")&'
   ....:          ' (Weight > 70))|'
   ....:          '((School == "Peking University")&'
   ....:          ' (Grade != "Senior")&'
   ....:          ' (Weight > 80))')
```


## 分组

    df.groupby(分组依据)[数据来源].使用操作
    
```
df.groupby('Gender')['Height'].median()

#按照条件分组
condition = df.Weight > df.Weight.mean() 
df.groupby(condition)['Height'].mean()

gb = df.groupby(['School', 'Grade'])
gb.ngroups #分组个数
res = gb.groups #获得映射字典
```

## 连接

![image.png-100.7kB](http://static.zybuluo.com/ShowArcher/5a853zcf7n7nsytxrsidr8w2/image.png)


### 值连接

merge

```
In [3]: df1 = pd.DataFrame({'Name':['San Zhang','Si Li'],
   ...:                     'Age':[20,30]})
   ...: 

In [4]: df2 = pd.DataFrame({'Name':['Si Li','Wu Wang'],
   ...:                     'Gender':['F','M']})
   ...: 

In [5]: df1.merge(df2, on='Name', how='left')
Out[5]: 
        Name  Age Gender
0  San Zhang   20    NaN
1      Si Li   30      F

#通过 suffixes 参数指定重复的列表

In [9]: df1 = pd.DataFrame({'Name':['San Zhang'],'Grade':[70]})

In [10]: df2 = pd.DataFrame({'Name':['San Zhang'],'Grade':[80]})

In [11]: df1.merge(df2, on='Name', how='left', suffixes=['_Chinese','_Math'])
Out[11]: 
        Name  Grade_Chinese  Grade_Math
0  San Zhang             70          80

```

### 索引连接

    join(on,how)
    on指索引名 how指方式

```
In [18]: df1 = pd.DataFrame({'Age':[20,30]},
   ....:                     index=pd.Series(
   ....:                     ['San Zhang','Si Li'],name='Name'))
   ....: 

In [19]: df2 = pd.DataFrame({'Gender':['F','M']},
   ....:                     index=pd.Series(
   ....:                     ['Si Li','Wu Wang'],name='Name'))
   ....: 

In [20]: df1.join(df2, how='left')
Out[20]: 
           Age Gender
Name                 
San Zhang   20    NaN
Si Li       30      F
```

### 方向连接

    concat(axis,join,keys)
    axis, join, keys ，分别表示拼接方向，连接形式，以及在新表中指示来自于哪一张旧表的名字
    axis:0表示纵向 1表示横向
    join=outer ，表示保留所有的列
    join=inner ，表示保留两个表都出现过的列
```
In [29]: df1 = pd.DataFrame({'Name':['San Zhang','Si Li'],
   ....:                     'Age':[20,30]})
   ....: 

In [30]: df2 = pd.DataFrame({'Name':['Wu Wang'], 'Age':[40]})

In [31]: pd.concat([df1, df2])
Out[31]: 
        Name  Age
0  San Zhang   20
1      Si Li   30
0    Wu Wang   40

In [35]: df2 = pd.DataFrame({'Name':['Wu Wang'], 'Gender':['M']})

In [36]: pd.concat([df1, df2])
Out[36]: 
        Name   Age Gender
0  San Zhang  20.0    NaN
1      Si Li  30.0    NaN
0    Wu Wang   NaN      M

In [37]: df2 = pd.DataFrame({'Grade':[80, 90]}, index=[1, 2])

In [38]: pd.concat([df1, df2], 1)
Out[38]: 
        Name   Age  Grade
0  San Zhang  20.0    NaN
1      Si Li  30.0   80.0
2        NaN   NaN   90.0

In [39]: pd.concat([df1, df2], axis=1, join='inner')
Out[39]: 
    Name  Age  Grade
1  Si Li   30     80
```


### plot画图

```
# 随机生成1000个数据
data = pd.Series(np.random.randn(1000),index=np.arange(1000))
 
# 为了方便观看效果, 我们累加这个数据
data.cumsum()

# pandas 数据可以直接观看其可视化形式
data.plot()

plt.show()


data = pd.DataFrame(
    np.random.randn(1000,4),
    index=np.arange(1000),
    columns=list("ABCD")
    )
data.cumsum()
data.plot()
plt.show()

# 将之下这个 data 画在上一个 ax 上面 设置参数
data.plot.scatter(x='A',y='C',color='LightGreen',label='Class2',ax=ax)
plt.show()
```












  [1]: 