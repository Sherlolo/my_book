# Opencv

标签（空格分隔）： study

[TOC]

---


# 图片和视频的基本操作

## 图片的读取

PIL读取的是image格式则为RGB格式。
采取imread读取图片，读取的格式为ndarray格式，且通道为BGR,读取有三种方式：

- 1 彩色图像 
- 0 灰度图像 
- -1 包含alpha通道的图片

```
import numpy as np
import cv2 as cv
from matplotlib import pyplot as plt

img = cv.imread('test.jpg', 0) #1 彩色图像 0 灰度图像 -1 4通道包含alpha
cv.imshow('image', img)
cv.waitKey(0)
cv.destroyAllWindows()

```

利用plot打开图片

```
#利用matplot打开图片
img = cv.imread('test.jpg', 0)
plt.imshow(img, cmap = 'gray', interpolation = 'bicubic')
plt.xticks([]), plt.yticks([]) #隐藏刻度值
plt.show()
```

## 视频的读取


```
#视频的打开
cap = cv.VideoCapture('test.mp4')
while cap.isOpened():
    ret, frame = cap.read() #返回读取成功否和帧
    if not ret:
        print('error read frame')
        break
    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY) #改变通道信息
    cv.imshow('frame', gray)
    if cv.waitKey(1) == ord('q'): #按键q会推出
        break
cap.release()
cv.destroyAllWindows()
```

## 图像的绘图功能

### 绘制线

    cv.line(img, (0, 0), (511, 511), (255, 0, 0), 5)
    
>参数依次为图片、起点坐标、终点坐标、颜色、厚度

### 画矩形

    cv.rectangle(img, (384, 0), (510, 128), (0, 255, 0), 3)
    
>参数依次为图片、左上角坐标、右下角指标、颜色、厚度
    
### 绘制圆形

    cv.circle(img, (447, 63), 53, (0, 0, 255), -1)

>参数依次为图片、圆心坐标、半径、颜色、是否填充

### 添加文字

    cv.putText(img, 'sherlolo', (10, 500), cv.FONT_HERSHEY_SIMPLEX, 4, (255,255,255), 2, cv.LINE_AA)
    
>参数依次为图片、文字、坐标、字体类型、字体大小、颜色、厚度、线条类型


### 例子

```
import numpy as np
import cv2 as cv

img = np.zeros((512, 512, 3), np.uint8)

#绘制厚度为5的线
cv.line(img, (0, 0), (511, 511), (255, 0, 0), 5)
#绘制矩形 需要左上角和右下角坐标
cv.rectangle(img, (384, 0), (510, 128), (0, 255, 0), 3)
#绘制圆形 圆心和半径 填充与否
cv.circle(img, (447, 63), 53, (0, 0, 255), -1)
#添加文本
cv.putText(img, 'sherlolo', (10, 500), cv.FONT_HERSHEY_SIMPLEX, 4, (255,255,255), 2, cv.LINE_AA)
cv.imshow('image', img)
cv.waitKey(0)
cv.destroyAllWindows()
```


## 鼠标作为画笔

鼠标的移动通过回调函数返回各种事件，再对事件进行响应做出操作

```
#鼠标作为画笔 鼠标回调函数
#通过回调函数 每次鼠标的执行都会返回一个事件
def draw_circle(event, x, y, flags, param):
    if event == cv.EVENT_LBUTTONDOWN:
        cv.circle(img, (x,y), 100, (255,0,0), -1)

img = np.zeros((512,512, 3), np.uint8)
cv.namedWindow('image')
cv.setMouseCallback('image', draw_circle)
while(1):
    cv.imshow('image', img)
    if cv.waitKey(20) == ord('q'):
        break
cv.destroyAllWindows()
```

拖动鼠标绘制矩形或圆形

```
#拖动鼠标绘制矩形或圆形
draw_flag = False #如果按下鼠标 则为真
mode = True #如果为真 绘制矩形 否则为圆形
ix, iy = -1, -1 #起点

def draw_circle(event, x, y, flags, param): #函数里的img调用了外部的img
    global ix,iy,draw_flag,mode
    if event == cv.EVENT_LBUTTONDOWN:
        draw_flag = True
        ix, iy = x, y
    elif event == cv.EVENT_MOUSEMOVE:
        if draw_flag == True:
            if mode == True:
                cv.rectangle(img, (ix,iy), (x,y), (0,255,0), -1)
            else:
                cv.circle(img, (x, y), 5, (0, 0, 255), -1)
    elif event == cv.EVENT_LBUTTONUP:
        draw_flag = False
        if mode == True:
            cv.rectangle(img, (ix, iy), (x, y), (0, 255, 0), -1)
        else:
            cv.circle(img, (x, y), 5, (0, 0, 255), -1)

img = np.zeros((512,512, 3), np.uint8)
cv.namedWindow('image')
cv.setMouseCallback('image', draw_circle)
while(1):
    cv.imshow('image', img)
    if cv.waitKey(20) == ord('q'):
        break
cv.destroyAllWindows()
```

## 图像的基本操作

### 图像的切片操作

Numpy是用于快速数组计算的优化库。因此，简单地访问每个像素值并对其进行修改将非常缓慢。但是可以进行切片操作

要想访问单个像素，可以才用.item方法


```
img[100,100] = [255,255,255] #改变像素值

#更好的访问元素
img.item(10,10,2)
img.itemset((10,10,2), 100)
img.item(10,10,2)

#拆分通道
b = img[:,:,0]
```

### 图像边框的填充

    cv.copyMakeBorder(src, top, bottom, left, right, borderType, dst=None, value=None)
    
参数说明：

- src - 输入图像
- top，bottom，left，right 边界宽度（以相应方向上的像素数为单位）
- borderType - 定义要添加哪种边框的标志。它可以是以下类型：
- cv.BORDER_CONSTANT - 添加恒定的彩色边框。该值应作为下一个参数给出。
- cv.BORDER_REFLECT - 边框将是边框元素的镜像，如下所示： fedcba | abcdefgh | hgfedcb
- **cv.BORDER_REFLECT_101**或 **cv.BORDER_DEFAULT**与上述相同，但略有变化，例如： gfedcb | abcdefgh | gfedcba
**cv.BORDER_REPLICATE**最后一个元素被复制，像这样： aaaaaa | abcdefgh | hhhhhhh
**cv.BORDER_WRAP**难以解释，它看起来像这样： cdefgh | abcdefgh | abcdefg

- value -边框的颜色，如果边框类型为**cv.BORDER_CONSTANT**

```
#实例代码
import cv2 as cv
import numpy as np
from matplotlib import pyplot as plt
BLUE = [255,0,0]
img1 = cv.imread('opencv-logo.png')
replicate = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REPLICATE)
reflect = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REFLECT)
reflect101 = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_REFLECT_101)
wrap = cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_WRAP)
constant= cv.copyMakeBorder(img1,10,10,10,10,cv.BORDER_CONSTANT,value=BLUE)
plt.subplot(231),plt.imshow(img1,'gray'),plt.title('ORIGINAL')
plt.subplot(232),plt.imshow(replicate,'gray'),plt.title('REPLICATE')
plt.subplot(233),plt.imshow(reflect,'gray'),plt.title('REFLECT')
plt.subplot(234),plt.imshow(reflect101,'gray'),plt.title('REFLECT_101')
plt.subplot(235),plt.imshow(wrap,'gray'),plt.title('WRAP')
plt.subplot(236),plt.imshow(constant,'gray'),plt.title('CONSTANT')
plt.show()
```

## 图像的算数运算

### 图像加法

OpenCV加法和Numpy加法之间有区别。OpenCV加法是饱和运算，而Numpy加法是模运算。

对于图像来说，饱和运算将提供更好的结果

```
x = np.uint8([250])
y = np.uint8([10])
print( cv.add(x,y) ) # 250+10 = 260 => 255
[[255]]
print( x+y )          # 250+10 = 260 % 256 = 4
[4]
```

### 图像融合

这也是图像加法，但是对图像赋予不同的权重，以使其具有融合或透明的感觉。根据以下等式添加图像：

$$dst = \alpha \times img1 + \beta \times img2 + \gamma $$

注意$\alpha + \beta = 1$ 

    cv.addWeighted(src1, alpha, src2, beta, gamma, dst=None, dtype=None)
    
### 按位运算

按位运算包括AND、OR、NOT、XOR四种操作，它们在提取图像的任何部分、定义和处理非矩形 ROI 等方面非常有用。

我们利用如下几个函数将Opencv徽标放置到背景图上。

```
#掩码是一个二值图像 白色放置目标图像 黑色保留
cv.bitwise_and(roi,roi,mask = mask_inv) #对roi和roi进行与运算 再放在掩码上

#二值处理 获得掩模
ret, dst = cv2.thresh(src, thresh, maxval, type)

参数说明， src表示输入的图片， thresh表示阈值， maxval表示最大值， type表示阈值的类型

返回阙值 和目标图像

type的类型

    1. cv2.THRESH_BINARY   表示阈值的二值化操作，大于阈值使用maxval表示，小于阈值使用0表示

    2. cv2.THRESH_BINARY_INV  表示阈值的二值化翻转操作，大于阈值的使用0表示，小于阈值的使用最大值表示

    3. cv2.THRESH_TRUNC    表示进行截断操作，大于阈值的使用阈值表示，小于阈值的不变

    4. cv2.THRESH_TOZERO   表示进行化零操作，大于阈值的不变，小于阈值的使用0表示

    5. cv2.THRESH_TOZERO_INV  表示进行化零操作的翻转，大于阈值的使用0表示，小于阈值的不变

```


提取opencv图标放置在背景图上

```
img1 = cv.imread('test2.jpg')
img2 = cv.imread('open-cv.png')

#创建ROI（感兴趣区域）
rows, cols, channels = img2.shape
roi = img1[0:rows, 0:cols]

#创建logo的掩码，并同时创建其相反掩码
img2gray = cv.cvtColor(img2, cv.COLOR_BGR2GRAY)
ret, mask = cv.threshold(img2gray, 10, 255, cv.THRESH_BINARY) #二值化处理 0为黑 255为白
mask_inv = cv.bitwise_not(mask) #取反 求反掩码

#将ROI中log的区域涂黑
img1_bg = cv.bitwise_and(roi, roi, mask = mask_inv)
#从logo图像中提取logo区域
img2_fg = cv.bitwise_and(img2, img2, mask = mask)
#将logo放入ROI中并修改图像
dst = cv.add(img1_bg, img2_fg)
img1[0:rows, 0:cols] = dst
cv.imshow('res', img1)
cv.waitKey()
cv.destroyAllWindows()
```

### opencv的优化

    cv.useOptimized() #检测是否启用优化
    cv.setUseOptimized(False) #设置是否启用优化
    
## opecv的图像处理

### 改变颜色空间

改变图像的颜色空间，BGR->GRAY BGR->HSV

    cv.cvtColor(input_image, flag)
    
取一定范围的图像做掩模

    cv.inRange(src, lowerb, upperb, dst=None) #在值范围内的为白色 否则为黑色
    
    
### 图像几何变换

#### 缩放

    resize(InputArray src, OutputArray dst, Size dsize, 
        double fx=0, double fy=0, int interpolation=INTER_LINEAR )
        
- dsize 输出图像大小
- fx x轴的缩放系数
- fy y轴的缩放系数
- interpolation：这个是指定插值的方式
    ·CV_INTER_NN - 最近-邻居插补
    ·CV_INTER_LINEAR - 双线性插值（默认方法）
    ·CV_INTER_AREA - 像素面积相关重采样。当缩小图像时，该方法可以避免波纹的出现。当放大图像时，类似于方法CV_INTER_NN。
    ·CV_INTER_CUBIC - 双三次插值。

```
#缩放
img = cv.imread('test.jpg')
res = cv.resize(img, None, fx=2, fy=2, interpolation=cv.INTER_CUBIC) #利用系数缩放
res1 = cv.resize(img,(500,500),interpolation=cv.INTER_CUBIC)    #利用输出尺寸缩放
cv.imshow('img',res1)
cv.waitKey()
cv.destroyAllWindows()
```

>注意：平移、旋转、仿射变换、透视变换都是通过变换矩阵来实现的


#### 平移

    cv2.warpAffine(src, M, dsize[, dst[, flags[, borderMode[, borderValue]]]]) →dst
    

- src - 输入图像。
- M - 变换矩阵。
- dsize - 输出图像的大小。
- flags - 插值方法的组合（int 类型！）
- borderMode - 边界像素模式（int 类型！）
- borderValue - （重点！）边界填充值; 默认情况下，它为0。

>图像变换矩阵

变换矩阵M
![image.png-2.5kB](http://static.zybuluo.com/ShowArcher/rwtuouvhpfyjxecb5fiotp0f/image.png)

opencnv的变换矩阵M
![image.png-3.3kB](http://static.zybuluo.com/ShowArcher/vobrw6qln011mjuf1fabfjuz/image.png)


```
img = cv.imread('test.jpg')
rows, cols, _ = img.shape

M = np.float32([[1,0,100],[0,1,50]]) #往右下偏移(100,50)
dst = cv.warpAffine(img, M, (cols,rows))
cv.imshow('img', dst)
cv.waitKey()
cv.destroyAllWindows()
```


#### 旋转

    M = cv2.getRotationMatrix2D(center, angle, scale)
    
- center：图片的旋转中心
- angle：旋转角度
- scale：旋转后图像相比原来的缩放比例
- M:计算得到的旋转矩阵   

```
img = cv.imread('test.jpg')
rows, cols, _ = img.shape
M = cv.getRotationMatrix2D(((cols-1)/2.0, (rows-1)/2.0),90,1)
dst = cv.warpAffine(img, M, (cols,rows))
cv.imshow('img',dst)
cv.waitKey()
cv.destroyAllWindows()
```

#### 仿射变换

```
#仿射变换 选择三个点 根据三个点变换到下一个平面
img = cv.imread('draw_line.png')
rows, cols, _ = img.shape
pts1 = np.float32([[35,35],[139,35],[35,140]])
pts2 = np.float32([[10, 100],[200, 50],[100, 250]])
M = cv.getAffineTransform(pts1, pts2) #获取仿射变换的变换矩阵
dst = cv.warpAffine(img, M, (cols,rows))

plt.subplot(121),plt.imshow(img),plt.title('input')
plt.subplot(122),plt.imshow(dst),plt.title('output')
plt.show()
```

#### 透视变换

```
#透视变换 将不共面的四个点通过透视变换改到一个平面
img = cv.imread('text.jpg')
rows, cols,ch = img.shape
pts1 = np.float32([[213,497], [739,303], [275,1495], [927,1439]])
pts2 = np.float32([[0,0], [300,0], [0,300], [300,300]])
M = cv.getPerspectiveTransform(pts1, pts2)
dst = cv.warpPerspective(img, M, (300, 300))
plt.subplot(121)
plt.imshow(img)
plt.title('Input')

plt.subplot(122)
plt.imshow(dst)
plt.title('Output')
plt.show()
```

## opecv 图像滤波

边界填充
这其中BorderType有四种

BORDER_DEFAULT - （边界默认）openCV中默认的处理方法，自动填充图像边界（映像一样） 32|123

BORDER_CONSTANT – （边界常数）填充边缘用指定像素值，使用常数填充边界。

BORDER_REPLICATE – （边界复制）填充边缘像素用已知的边缘像素值，复制原图中最临近的行或者列。

BORDER_WRAP – （边界包装）用另外一边的像素来补偿填充。


### 卷积

    CV_EXPORTS_W void filter2D( InputArray src, OutputArray dst, int ddepth,
                            InputArray kernel, Point anchor=Point(-1,-1),
                            double delta=0, int borderType=BORDER_DEFAULT );
                            
- InputArray src: 输入图像
- OutputArray dst: 输出图像，和输入图像具有相同的尺寸和通道数量
- int ddepth: 目标图像深度 -1表示深度相同

```
img = cv.imread('open-cv.png')
kernel = np.ones((5,5), np.float32)/25 #卷积核
dst = cv.filter2D(img, -1, kernel)
fig, ax = plt.subplots(nrows=1,ncols=2) #1行2列 不能使用二维坐标
ax[0].imshow(img)
ax[0].set(title='input')

ax[1].imshow(dst)
ax[1].set(title='output')
plt.show()
```

### 均值滤波

    cv2.blur(img,ksize)
    
- img：原图像
- ksize：核大小
- 原理：它只取内核区域下所有像素的平均值并替换中心元素。3x3标准化的盒式过滤器如下所示：
- 特征：核中区域贡献率相同。
- 作用：对于椒盐噪声的滤除效果比较好。

```
#图像模糊 图像平均
img = cv.imread('open-cv.png')
blur = cv.blur(img, (5,5))
fig, ax = plt.subplots(1,2)

ax[0].imshow(img)
ax[0].set(title='input')
ax[1].imshow(blur)
ax[1].set(title='output')

plt.show()
```

### 高斯滤波

卷积核是一个高斯分布的二维矩阵，在通过计算求出其平均值

    void cv::GaussianBlur

- InputArray	src,	（原始图像：channels不限，各通道单独处理；depth应当是CV_8U，CV_16U，CV_16S，CV_32F或CV_64F）
- OutputArray	dst,	（目标图像：与原始图像size和type一致）
- Size	ksize,	（高斯核大小，ksize.width和ksize.height可以不同，但是都必须为正的奇数（或者为0，此时它们的值会自动由sigma进行计算））
- double	sigmaX,	（高斯核在x方向的标准差）
- double	sigmaY=0,	（高斯核在y方向的标准差（sigmaY=0时，其值自动由sigmaX确定（sigmaY=sigmaX）；sigmaY=sigmaX=0时，它们的值将由ksize.width和ksize.height自动确定））
int	borderType=BORDER_DEFAULT	（像素外插策略，可参考BorderTypes）


```
img = cv.imread('open-cv.png')
blur = cv.GaussianBlur(img,(5,5),0)
fig, ax = plt.subplots(1,2)
```

### 中位模糊

```
img = cv.imread('open-cv.png')
median = cv.medianBlur(img,5)
fig, ax = plt.subplots(1,2)
```


## 形态变换 腐蚀和膨胀

腐蚀和膨胀都是通过卷积来操作。

### 腐蚀

原理：对图像做为卷积乘积后，取最小的元素作为输出
应用：边界附近的所有像素都会被丢弃。因此，它有助于去除小的白色噪声

    erode(src, kernel, dst=None, anchor=None, iterations=None, borderType=None, borderValue=None):

- anchor: 结构元素的锚点位置，默认值value(-1,-1)表示锚点位于结构元素中心

### 膨胀

原理：对图像做为卷积乘积后，取最大的元素作为输出
应用：通常，在消除噪音的情况下，腐蚀后会膨胀。或者去除目标里面的噪声

    dilate(src, kernel, dst=None, anchor=None, iterations=None, borderType=None, borderValue=None)
    
- anchor: 结构元素的锚点位置，默认值value(-1,-1)表示锚点位于结构元素中心

```
#腐蚀和膨胀
img = cv.imread('j.png',0)
kernel = np.ones((5,5), np.uint8)
erosion = cv.erode(img, kernel, iterations=1)
dilation = cv.dilate(img,kernel, iterations=1)
dilation_new = cv.dilate(erosion,kernel, iterations=1)

# cv.imshow('input', img)
# cv.imshow('output', erosion)
# cv.waitKey()

fig, axes = plt.subplots(2,2)
axes[0,0].imshow(img)
axes[0,1].imshow(erosion)
axes[1,0].imshow(dilation)
axes[1,1].imshow(dilation_new)
plt.show()
```

### 开运算和闭运算

    morphologyEx(src, op, kernel, dst=None, anchor=None, iterations=None, borderType=None, borderValue=None)
    
- cv2.MORPH_OPEN	开运算(open) ,先腐蚀后膨胀的过程。开运算可以用来消除小黑点，在纤细点处分离物体、平滑较大物体的边界的 同时并不明显改变其面积。
- cv2.MORPH_CLOSE	闭运算(close)，先膨胀后腐蚀的过程。闭运算可以用来排除小黑洞。
- cv2.MORPH_GRADIENT 形态学梯度(morph-grad)，可以突出团块(blob)的边缘，保留物体的边缘轮廓。
- cv2.MORPH_TOPHAT	顶帽(top-hat)，将突出比原轮廓亮的部分。 输入图像和图像开运算之差
- cv2.MORPH_BLACKHAT	黑帽(black-hat)，将突出比原轮廓暗的部分。输入图像和图像闭运算之差


## 图像金字塔

我们将需要创建一组具有不同分辨率的相同图像，并在所有图像中搜索对象。这些具有不同分辨率的图像集称为“图像金字塔”（因为当它们堆叠在底部时，最高分辨率的图像位于顶部，最低分辨率的图像位于顶部时，看起来像金字塔）。

    cy.pyrDown() 和 cy.pyrUp()
    对图像分辨进行缩放
    
### 高斯金字塔

  高斯金字塔中的较高级别（低分辨率）是通过删除较低级别（较高分辨率）图像中的连续行和列而形成的。然后较高级别的每个像素由基础级别的5个像素的贡献与高斯权重形成.因此面积减少到原始面积的四分之一。
  
### 拉普拉斯金字塔

拉普拉斯金字塔由高斯金字塔形成。没有专用功能。拉普拉斯金字塔图像仅像边缘图像。它的大多数元素为零。它们用于图像压缩。拉普拉斯金字塔的层由高斯金字塔的层与高斯金字塔的高层的扩展版本之间的差形成.


### 利用金字塔进行特征融合

```
import cv2 as cv
import numpy as np,sys
A = cv.imread('apple.jpg')
B = cv.imread('orange.jpg')
# 生成A的高斯金字塔
G = A.copy()
gpA = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpA.append(G)
# 生成B的高斯金字塔
G = B.copy()
gpB = [G]
for i in range(6):
    G = cv.pyrDown(G)
    gpB.append(G)


# 生成A的拉普拉斯金字塔
lpA = [gpA[5]]
for i in range(5,0,-1):
    GE = cv.pyrUp(gpA[i])
    L = cv.subtract(gpA[i-1],GE)
    lpA.append(L)
# 生成B的拉普拉斯金字塔
lpB = [gpB[5]]
for i in range(5,0,-1):
    GE = cv.pyrUp(gpB[i])
    L = cv.subtract(gpB[i-1],GE)
    lpB.append(L)
# 现在在每个级别中添加左右两半图像
LS = []
for la,lb in zip(lpA,lpB):
    rows,cols,dpt = la.shape
    ls = np.hstack((la[:,0:cols//2], lb[:,cols//2:]))
    LS.append(ls)
# 现在重建
ls_ = LS[0]
for i in range(1,6):
    ls_ = cv.pyrUp(ls_)
    ls_ = cv.add(ls_, LS[i])
# 图像与直接连接的每一半
real = np.hstack((A[:,:cols//2],B[:,cols//2:]))
cv.imwrite('Pyramid_blending2.jpg',ls_)
cv.imwrite('Direct_blending.jpg',real)
##
```

![image.png-700.8kB](http://static.zybuluo.com/ShowArcher/xnj5mrien4c0y0jmvn3icnvg/image.png)








ddddd


  [1]: 


  [1]: 


  [1]: 