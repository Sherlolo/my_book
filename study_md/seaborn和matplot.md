# seaborn和matplot

标签（空格分隔）： study

---

[TOC]

# 画图快捷使用

```
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns

x = np.arange(1, 10)
y = x*2

#同一画板上 分割多个
fig = plt.figure()
ax1 = fig.add_subplot(221)
ax1.set(xlabel='x', ylabel='y')
ax1.plot(x, y)

ax2 = fig.add_subplot(222)
ax2.set(xlabel='x2', ylabel='y2')
ax2.scatter(x, y)
plt.show()



#画多个画板 如果前一个plt.show（）不关闭 后一个就不会出现
fig, axes = plt.subplots(2,2)
axes[0,0].plot(x,y)
axes[1,1].scatter(x,y)
plt.show()

fig = plt.figure()
plt.plot(x,y)
plt.show()


#searborn画图
fig, axes = plt.subplots(2,2)

plt.subplot(221)
sns.distplot(x)
plt.subplot(222)
sns.distplot(y)

plt.show()
```


# Seaborn介绍

    我们使用了Matplotlib，这是一个广泛实现的2D绘图库。 同样，Seaborn是Python中的可视化库。 它建立在Matplotlib之上。如果Matplotlib“试图让事情变得轻松而艰难，那么Seaborn也试图让一套定义明确的事情变得容易。”它旨在作为补充，而不是替代
    
## seaborn特点

- 内置主题的样式matplotlib图形
- 可视化单变量和双变量数据
- 拟合并可视化线性回归模型
- 绘制统计时间序列数据
- Seaborn与NumPy和Pandas数据结构配合良好
- 它内置了Matplotlib图形样式的主题

## seaborn的背景显示

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sb
def sinplot(flip = 1):
    x = np.linspace(0, 14, 100)
    for i in range(1, 5):
        plt.plot(x, np.sin(x+i*.5) * (7 - i)*flip)

sb.set() #设置以seaborn显示图例 
sb.set_style('darkgrid') #设置背景格式 默认darkgrid
sinplot()
plt.show()
```

>matplot格式：
![image.png-30.1kB](http://static.zybuluo.com/ShowArcher/v4ohna76csu887jubeslwpnv/image.png)

>seaborn格式：
![image.png-50.3kB](http://static.zybuluo.com/ShowArcher/0ar8azj82ff5ulgazhffcvmy/image.png)


    sb.set_style(para)
### 关于背景格式的设置，有如下参数：

- Darkgrid 黑色加栅格
- Whitegrid 白色加栅格
- Dark 单纯的黑色
- White 单纯的白色
- Ticks 显示横纵坐标刻度
- 其他自定义的参数 详细可以查看[文档](https://iowiki.com/seaborn/seaborn_quick_guide.html)


### 去除右上角和顶端的坐标轴

    sb.set_style('ticks')
    sinplot()
    sb.despine(）#该方法需用在plot画图之后
    plt.show()

### 设置图例的大小

    sb.set_context(para)
    para: paper, notebook, talk, poster
    
![image.png-248.5kB](http://static.zybuluo.com/ShowArcher/61fjfojlkbktotjr7brq7svn/image.png)



## seaborn的颜色

```
current_palette = sb.color_palette()
sb.palplot(sb.color_palette("Greens"))
plt.show()

#假设绘制范围从-1到1的数据。从-1到0的值采用一种颜色，0到+1采用另一种颜色。
current_palette = sb.color_palette()
sb.palplot(sb.color_palette("BrBG", 7))
plt.show()
```

# 常见的绘图

## 绘制核密度图

```
#绘制直方图 离散
sb.distplot(df['petal_length'],kde = False)
plt.show()

#绘制核密度图 连续
sb.distplot(df['petal_length'],hist=False)
plt.show()

#将两者同时输出
sb.distplot(df['petal_length'])
plt.show()
```

![image.png-11.8kB](http://static.zybuluo.com/ShowArcher/elwvh70o86d106szdtpl3ze4/image.png)


![image.png-21.6kB](http://static.zybuluo.com/ShowArcher/0to485wj0bm4f02mv3fiv7mf/image.png)



## 绘制双边量分布图

投影两个变量之间的双变量关系，以及每个变量在不同轴上的单变量分布。

```
df = sb.load_dataset('iris')
sb.jointplot(x = 'petal_length',y = 'petal_width',data = df)
#sb.jointplot(x = 'petal_length',y = 'petal_width',data = df, kind='hex') #绘制六边形图
plt.show()
```


![image.png-19.9kB](http://static.zybuluo.com/ShowArcher/5k9lnqw8bbs6ddlgr3yvh20g/image.png)


## 绘制多个变量分布图

要在数据集中绘制多个成对的双变量分布，可以使用pairplot()

    seaborn.pairplot(data,…)
    
参数：

- data 数据帧
- hue 数据变量所映射的颜色
- palette 用于映射色调变量的颜色集
- kind 绘制类型 {'scatter'，'reg'}
- diag_kind 对角线上的绘制类型 {'hist'，'kde'}


## 绘制散点图

```
sb.stripplot(x = "species", y = "petal_length", data = df)
sb.stripplot(x = "species", y = "petal_length", data = df, jitter = Ture) #jitter加入一点抖动
sb.swarmplot(x = "species", y = "petal_length", data = df) #定位在分类轴上
```

## 绘制箱型图

![image.png-44.6kB](http://static.zybuluo.com/ShowArcher/i5u67snkq7eardyqny2bumza/image.png)

箱形图通常具有从箱子延伸的垂直线，其被称为晶须。 这些晶须表明在上下四分位数之外的可变性，因此Box Plots也被称为box-and-whisker图和box-and-whisker图。 数据中的任何异常值都被绘制为单个点。

```
sb.boxplot(x = "species", y = "petal_length", data = df)
```

## 绘制小提琴图

小提琴图是箱形图与核密度估计的组合。 因此，这些图更容易分析和理解数据的分布。小提琴曲线使用KDE，小提琴的较宽部分表示较高的密度，较窄的区域表示相对较低的密度。 箱形图中的四分位数范围和kde中的较高密度部分落在每类小提琴图的相同区域中。

![image.png-59kB](http://static.zybuluo.com/ShowArcher/4ucicycm7krmtcoj9vrviosn/image.png)

注：其中归与直线的可以理解为异常值


## 绘制条形图

barplot()显示分类变量（离散）和连续变量之间的关系。数据条的长度表示该类别中数据的比例，每个矩形的高度表示平均值。

    sb.barplot(x = "sex", y = "survived", hue = "class", data = df)
    指定hue对已分组的数据进行嵌套分组(第二次分组)并绘制条形图
    
条形图中的一个特例是显示每个类别中的观察结果，而不是计算第二个变量的统计数据。 为此，我们使用countplot().

    sb.countplot(x = " class ", data = df, palette = "Blues");

## 点图

点图与条形图相同，但风格不同。 而不是完整条，估计值由另一轴上某个高度处的点表示。

    sb.pointplot(x = "sex", y = "survived", hue = "class", data = df)
    sns.scatterplot(x=feature, y='pressure', hue='pressure', palette='Blues', data=train) #表示根据hue生成分布
    
## 传递dataframe对象

    sb.boxplot(data = df, orient = "h")
    
## 绘制热力图

热力图体现了两个离散变量之间的组合关系
热力图，有时也称之为交叉填充表。该图形最典型的用法就是实现列联表的可视化，即通过图形的方式展现两个离散变量之间的组合关系。

```
def show_features_corr(train):
    corr = train.corr()
    plt.subplots(figsize=(15,12))
    sns.heatmap(corr, vmax=0.9, cmap="Blues", square=True)
    plt.show()

```




```
import pandas as pd
import seaborn as sb
from matplotlib import pyplot as plt
df = sb.load_dataset('tips')
sb.violinplot(x = "day", y = "total_bill", data=df)
plt.show()
```







```
from sklearn.metrics import confusion_matrix
import numpy as np
import seaborn as sn
import matplotlib.pyplot as plt

y_true = np.ones([1,1000]).squeeze()
y_true[602:1000] = 0
y_pred = np.ones([1,1000]).squeeze()
y_pred[706:1000] = 0


a = confusion_matrix(y_true, y_pred)

f, ax= plt.subplots()
sn.heatmap(a, annot=True, fmt='.20g', cmap=plt.cm.Blues)
ax.set_title('test1')
ax.set_ylabel('Y True')
ax.set_xlabel('X True')
plt.show()
```





  [1]: 


  [1]: 


  [1]: 


  [1]: 