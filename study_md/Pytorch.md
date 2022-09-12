# Pytorch

标签：study 

---



[TOC]

## 安装pytorch和pycharm

在jupyter notebook中使用其他环境,需在其他环境运行如下指令

    conda install nb_conda #在base环境安装
    pip install ipykernel #在其他环境安装
    
## 1 torch和jupyter基本使用

> python学习工具函数

```
dir() #打开包里的功能库 最后会迭代到'__avialiable__' 表示此时是一个方法或函数
help() #展开方法或函数的说明

dir(torch)
help(torch.cuda.cuda.is_available)
```
> jupyter
输入如下代码，可显示原函数

    dateset??
    
### 1.1 数据读取

1. dateset 提供一种方式读取数据及其label
2. dataleader 为后面的网络提供不同的数据形式

## 2 tensorboard的使用

### 2.1 tensorboard基础(SummaryWriter类)

>读取数据并保存日志文件

```
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('logs')
for i in range(100):
    writer.add_scalar('y= 2x', 2*i, i) # y x
writer.close()
```
注意对同一标题的日志进行写，会出现视图错误

>查看日志文件

    tensorboard --logdir=logs --port=6007 #port代表指定的端口 默认6006
    
> 读入图片

```
image_path = 'train/ants_image/5650366_e22b7e1065.jpg'
img_PIL = Image.open(image_path)
img_array = np.array(img_PIL) #ndarray的格式为HWC
writer.add_image('test', img_array, 1, dataformats = 'HWC') #1表示step1
```

## 2.2 tensor类型的使用

>利用transforms常见tensor类型

transforms是一个工具箱 可以用里面的工具创建tensor

```
img_path = 'train/ants_image/0013035.jpg'
img = Image.open(img_path)
tensor_trans = transforms.ToTensor()
tensor_img = tensor_trans(img)

tensor_img = transforms.ToTensor(img) #错误用法
```
> ctrl + p可以查看输入参数

## 3 transoforms 类的使用


```
from PIL import Image
from torch.utils.tensorboard import SummaryWriter
from torchvision import transforms

img_path = 'train/ants_image/0013035.jpg'
img = Image.open(img_path)

writer = SummaryWriter('logs')

#totensor
trans_totensor = transforms.ToTensor()
tensor_img = trans_totensor(img)
writer.add_image('tensor_img', tensor_img)

#Normalize 归一化
#input:PIL img 
#output: tensor
trans_norm = transforms.Normalize([0.5, 0.5, 0.5],[0.5, 0.5, 0.5])
img_norm = trans_norm(tensor_img)
writer.add_image('tensor_Normalize', img_norm)

```

### Resize 进行等比例缩放

```
#input:PIL img 
#output: PIL img
print(img.size)
trans_resize = transforms.Resize((512, 512))
# img PIL -> resize -> img_resize tensor
img_resize = trans_resize(img)
# img_resize PIL -> totensor -> img_resize tensor
img_resize = trans_totensor(img_resize)
writer.add_image('resize', img_resize, 0)
print(img_resize)

#compose多种方式的图片处理

#compose 1 resize 缩放
#input:PIL img 
#output: tensor
trans_resize_2 = transforms.Resize((512, 1200))
# PIL -> PIL -> tensor
trans_compose = transforms.Compose([trans_resize_2, trans_totensor])
img_compose = trans_compose(img)
writer.add_image('compose', img_compose, 2)

#compose 2 RandomCrop 随机裁剪
#input:PIL img 
#output: tensor
trans_random = transforms.RandomCrop(512)
trans_compose_2 = transforms.Compose([trans_random, trans_totensor])
for i in range(10):
    img_crop = trans_compose_2(img)
    writer.add_image('randomcrop', img_crop, i)

writer.close()
```

## 4 dataset和dataloader

>介绍

dataset:建立一个数据集
dataloader:加载数据集 并对数据进行一定的结构化操作

### 4.1 dataset

```
import torchvision
from torch.utils.tensorboard import SummaryWriter


dataset_transform = torchvision.transforms.Compose([
    torchvision.transforms.ToTensor()
])

train_set = torchvision.datasets.CIFAR10(root='./dataset', train=True, transform=dataset_transform, download=True)
test_set = torchvision.datasets.CIFAR10(root='./dataset', train=False, transform=dataset_transform, download=True)

```

注意：如果下载失败 可以访问源地址下载

### 4.2 dataloader

```
import torchvision
from torch.utils.data import DataLoader
from torch.utils.tensorboard import SummaryWriter

test_data = torchvision.datasets.CIFAR10('./dataset', train = False, transform = torchvision.transforms.ToTensor())

test_loader = DataLoader(dataset = test_data, batch_size = 64, shuffle = True, num_workers = 0, drop_last = True)

writer = SummaryWriter('dataload')
for epoch in range(2):
    for i, data in enumerate(test_loader):
        img, target = data
        #print(img.shape)
        #print(target)
        writer.add_images("epoch:{}".format(epoch), img, i)
writer.close()
```

    test_loader = DataLoader(dataset = test_data, batch_size = 64, shuffle = True, num_workers = 0, drop_last = True)
    
- batch_size 代表批次
- shuffle 是否打乱
- num_workers 多进程
- drop_last 为真 对数据保存 为否 去尾法保留

## 5 module类的加载
torch.nn 表示基本网络结构
torch中网络的创建都以torch.nn.Module类为基础 进行继承修改
```
import torch
from torch import nn

class Decade(nn.Module):
    def __init__(self):
        super().__init__()

    def forward(self, input):
        output = input + 1
        return output

decade = Decade()
x = torch.tensor(1.0)
y = decade(x)
print(x, y)
```


## 6 卷积层

> 卷积的作用
    提取局部特征

> 图像参数

    (N, C, H, W)
    
- N代表batchsize, 即批次，表示以多少张图片为一张图进行处理
- C代表channel, 即通道数
- H代表Height, 即高度
- W代表Weight, 即宽度

### 6.1 二维卷积

Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)

- stride代表卷积核步长
- padding代表边缘填充

![image.png-86.3kB](http://static.zybuluo.com/ShowArcher/6gcbj178tii4obupjswr1m7n/image.png)

- dilation 表示感受野的扩张 默认为1 
 
```
import torch
import torchvision
from torch import nn
from torch.nn import Conv2d
from torch.utils.data import DataLoader
from torch.utils.tensorboard import SummaryWriter

dataset = torchvision.datasets.CIFAR10('./dataset', train = False, transform=torchvision.transforms.ToTensor(), download=True)

dataloader = DataLoader(dataset, batch_size=64)

class Decade(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)

    def forward(self, x):
        x = self.conv1(x)
        return x

decade = Decade()

writer = SummaryWriter('conv')

for i, data in enumerate(dataloader):
    imgs, target = data
    output = decade(imgs)
    print(imgs.shape)
    print(output.shape)
    writer.add_images('input', imgs, i)

    #[64, 6, 30, 30] -> [xxx, 3, 30, 30]
    output = torch.reshape(output, (-1, 3, 30, 30)) #-1自己计算大小
    writer.add_images('output', output, i)

writer.close()
```

## 7 池化层

### 7.1 最大池化 MaxPool

> 最大池化
    也叫下采样层，目的是压缩图像
    
> 方法使用

    torch.nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)
    
- stride: 步长和kernel_size大小默认一致
- ceil_mode: 表示保留还是去尾否


## 8 非线性激活

> 非线性函数
    常见的如ReLu, sigmod等，对变量进行非线性处理

```
#不替换
input = -1
ReLu(input, inplace = Flase)
input = 0

#替换
input = -1
output = ReLu(input, inplace = True)
input = -1
output = 0

```

> 使用

```
class Tudui(nn.Module):
    def __init__(self):
        super(Tudui, self).__init__()
        self.relu1 = ReLU()
        self.sigmoid1 = Sigmoid()

    def forward(self, input):
        output = self.sigmoid1(input)
        return output
```

## 9 线性层

```
class Tudui(nn.Module):
    def __init__(self):
        super(Tudui, self).__init__()
        self.linear1 = Linear(196608, 10)   #变换 [1,1,1,196608] ->[1,1,1,10]

    def forward(self, input):
        output = self.linear1(input)
        return output

tudui = Tudui()

for data in dataloader:
    imgs, targets = data
    print(imgs.shape)
    output = torch.flatten(imgs) #直接摊平成一维的
    print(output.shape)
    output = tudui(output)
    print(output.shape)
```
    
## 10 Sequential类(整合网络)

```
class Decade(nn.Module):
    def __init__(self):
        super().__init__()
        self.module1 = Sequential(
            Conv2d(3, 32, 5, padding=2),
            MaxPool2d(2),
            Conv2d(32, 32, 5, padding=2),
            MaxPool2d(2),
            Conv2d(32, 64, 5, padding=2),
            MaxPool2d(2),
            Flatten(),
            Linear(1024, 64),
            Linear(64, 10)
        )

    def forward(self, x):
        y = self.module1(x)
        return y
```

## 11 损失函数

> 介绍

用来表示预测值与真实值之间的误差,越小越好.另外在反向传播中也会利用损失函数更新梯度.

### 11.1 直接损失

有对损失值求和和求平均两种方式

```
loss = L1Loss(reduction='sum')
result = loss(inputs, targets)
print(result)
```

### 11.2 均方根误差

对损失值求其均方跟,也有求和和求平均两种方式.计算方式如下

$$loss = (y - x)2$$

```
loss_mse = nn.MSELoss()
result1 = loss_mse(inputs, targets)
print(result1)
```

### 11.3 交叉熵损失值

对于多分类问题,对应各个类别求出损失值. 可以理解为越不像其他类的概率

![交叉熵](http://static.zybuluo.com/ShowArcher/zdknkh4s4mzr2fbb71cuz1bl/image.png)

```
class Decade(nn.Module):
    def __init__(self):
        super(Tudui, self).__init__()
        self.model1 = Sequential(
            Conv2d(3, 32, 5, padding=2),
            MaxPool2d(2),
            Conv2d(32, 32, 5, padding=2),
            MaxPool2d(2),
            Conv2d(32, 64, 5, padding=2),
            MaxPool2d(2),
            Flatten(),
            Linear(1024, 64),
            Linear(64, 10)
        )

    def forward(self, x):
        x = self.model1(x)
        return x

loss = nn.CrossEntropyLoss()
decade = Decade()
for data in dataloader:
    imgs, targets = data
    outputs = decade(imgs)
    result_loss = loss(outputs, targets)
    #result_loss.backward() 反向传播 更新梯度
    print("ok")
```

## 12 优化器

在反向传播中，利用损失函数计算的损失值，在反向传播中更新梯度，再更新网络权重。

```
loss = nn.CrossEntropyLoss()
decade = Decade()
optim = torch.optim.SGD(decade.parameters(), lr=0.01)

for epoch in range(20):
    running_loss = 0.0
    for data in dataloader:
        imgs, targets = data
        outputs = decade(imgs)
        result_loss = loss(outputs, targets)
        optim.zero_grad() #初始化梯度为0
        result_loss.backward() #反向传播 更新梯度
        optim.step() #利用反向传播 更新网络权重
        running_loss += result_loss
    print(running_loss)
```

## 13 模型的使用

### 13.1 模型的加载和修改

对vgg16模型进行修改，添加一层或修改一层

```
from torch import nn
import torchvision

vgg16_false = torchvision.models.vgg16(pretrained=False)
vgg16_true = torchvision.models.vgg16(pretrained=True)
train_data = torchvision.datasets.CIFAR10('./dataset', train = True, transform = torchvision.transforms.ToTensor(), download = True)
print(vgg16_true)

#添加模型
vgg16_true.classifier.add_module('add_linear', nn.Linear(1000, 10))
print(vgg16_true)

#修改模型
vgg16_false.classifier[6] = nn.Linear(4096, 10)
print(vgg16_false)
```

### 13.2 模型的保存和读取

> 保存

推荐第二种方式，保存的模型数据小
```
vgg16 = torchvision.models.vgg16(pretrained=False)

# 保存方式1 模型结构和参数
torch.save(vgg16, 'vgg16_method1.pth')

# 保存方式2 模型参数
torch.save(vgg16.state_dict(), 'vgg16_method2.pth')
```

> 读取

```
# 方式1 直接加载模型
model = torch.load('vgg16_method1.pth')
print(model)

# 方式2 加载字典 再加载权重
vgg16 = torchvision.models.vgg16(pretrained=False)
vgg16.load_state_dict(torch.load('vgg16_method2.pth'))
print(vgg16)
```

### 13.3 陷阱

对于自己写的网络模型，需要添加其说明。

```
class Decade(nn.Module):
    def __init__(self):
        super(Tudui, self).__init__()
        self.conv1 = nn.Conv2d(3, 64, kernel_size=3)

    def forward(self, x):
        x = self.conv1(x)
        return x
        
model = torch.load('decade_method1.pth')
print(model)
```

## 14 完整的网络训练

model.py 存放网络结构
```
import torch
from torch import nn


class Decade(nn.Module):
    def __init__(self):
        super().__init__()
        self.model = nn.Sequential(
            nn.Conv2d(3, 32, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 32, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 64, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Flatten(),
            nn.Linear(1024, 64),
            nn.Linear(64, 10)
        )

    def forward(self, x):
        x = self.model(x)
        return x


if __name__ == '__main__':
    decade = Decade()
    input = torch.ones((64, 3, 32, 32))
    output = decade(input)
    print(output.shape)

```

> 开始网络训练

```
import torch
import torchvision
from torch import nn
from torch.utils.data import DataLoader
from torch.utils.tensorboard import SummaryWriter

from model import *

#准备数据集
train_data = torchvision.datasets.CIFAR10('./dataset', train=True, transform=torchvision.transforms.ToTensor(), download=True)
test_data = torchvision.datasets.CIFAR10('./dataset', train=False, transform=torchvision.transforms.ToTensor(), download=True)

#长度计算
length_train = len(train_data)
length_test = len(test_data)
print('训练集长度:{}'.format(length_train))
print('测试集长度:{}'.format(length_test))

#加载数据集
train_data = DataLoader(train_data, batch_size=64)
test_data = DataLoader(test_data, batch_size=64)

#创建网络
decade = Decade()

#配置网络参数
loss_fn = nn.CrossEntropyLoss() #损失函数
learning_rate = 0.01
optimizer = torch.optim.SGD(decade.parameters(), lr=learning_rate)
total_train_step = 0 #训练的次数
total_test_step = 0 #测试的次数
epoch = 10 #训练的论数

#添加tensorboard
writer = SummaryWriter('./log_train')

for i in range(10):
    print('第 {} 轮开始训练'.format(i + 1))

    '''
    #训练开始
    decade.train() #开启训练模式 会打开关闭一些网络结构
    for data in train_data:
        imgs, targets = data
        outputs = decade(imgs)
        loss = loss_fn(outputs, targets)

        #反向传播
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        total_train_step += 1
        if total_train_step % 100 == 0:
            print('训练次数: {}, loss: {}'.format(total_train_step, loss))
            writer.add_scalar('train_loss', loss.item(), total_train_step)
    '''

    #测试开始
    decade.eval()
    total_test_loss = 0
    total_accuracy = 0.0
    with torch.no_grad(): #在没有梯度的情况下进行测试
        for data in test_data:
            imgs, targets = data
            outputs = decade(imgs)
            loss = loss_fn(outputs, targets)
            total_test_loss += loss.item()
            accuracy = (outputs.argmax(1) == targets).sum()
            total_accuracy += accuracy
    print('测试结果 Loss: {} 准确率: {}'.format(total_test_loss, total_accuracy/length_test))
    total_test_step += 1
    writer.add_scalar('test_loss', total_test_loss, total_test_step)
    writer.add_scalar('test_accuracy', total_accuracy/length_test, total_test_step)

    torch.save(decade, 'decade_{}.pth'.format(i))
    print('模型已保存')

writer.close()


```

### 准确率的计算

针对分类任务

```
import torch

outputs = torch.tensor([0.1, 0.2],
                        [0.3, 0.4])
preds = outputs.argmax(1) #1 代表按行计算最大 0为列
targets = torch.tensor([0,1]) #真实类别
print((preds==targets).sum()) #计算出相等的个数
```


## 15 GPU训练
    
    nvidia-smi #查看显卡

### 15.1 第一种方式

将网络、损失函数、数据修改成gpu模式

```
tudui = Tudui() #修改网络
if torch.cuda.is_available():
    tudui = tudui.cuda()
    
loss_fn = nn.CrossEntropyLoss() #修改损失函数
if torch.cuda.is_available():
    loss_fn = loss_fn.cuda()
    
imgs, targets = data #修改数据
if torch.cuda.is_available():
    imgs = imgs.cuda()
    targets = targets.cuda()
```

### 15.2 第二种方式

添加到设备

```
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

tudui = Tudui()
tudui = tudui.to(device) #也可写成tudui.to(device) 不用返回

loss_fn = nn.CrossEntropyLoss()
loss_fn = loss_fn.to(device)

imgs, targets = data
imgs = imgs.to(device)
targets = targets.to(device)
```

device命令介绍

```
device = torch.device('cpu')
device = torch.device('gpu:0')  #多个显卡 选择第一个显卡
```

## 16 测试模型

```
import torch
import torchvision
from PIL import Image
from torch import nn

train_data = torchvision.datasets.CIFAR10('./dataset', train=False, transform=torchvision.transforms.ToTensor(), download=True)
dict_target = train_data.class_to_idx
dict_target = [indx for indx, vale in dict_target.items()]

image_path = "./imgs/airplane.png"
image = Image.open(image_path)
print(image)
image = image.convert('RGB')
transform = torchvision.transforms.Compose([torchvision.transforms.Resize((32, 32)),
                                            torchvision.transforms.ToTensor()])

image = transform(image)
print(image.shape)

class Decade(nn.Module):
    def __init__(self):
        super(Decade, self).__init__()
        self.model = nn.Sequential(
            nn.Conv2d(3, 32, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 32, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 64, 5, 1, 2),
            nn.MaxPool2d(2),
            nn.Flatten(),
            nn.Linear(64*4*4, 64),
            nn.Linear(64, 10)
        )

    def forward(self, x):
        x = self.model(x)
        return x

model = torch.load("decade_9.pth", map_location=torch.device('cpu')) #注意加载模型 要切换模式 和加入原来的代码
print(model)
image = torch.reshape(image, (1, 3, 32, 32))
model.eval()
with torch.no_grad():
    output = model(image)
max = output.argmax(1) #返回行最大值所在的下标
print(output, max)
print(dict_target[max])

```


## 17 网络权重初始化

网络权重的初始化默认采用Kaiming均匀初始化

```
with torch.no_grad():
    return tensor.uniform_(-bound, bound)
```

## 18 维度变换

```
a #（2.3）
tensor([[1, 2, 3],
        [3, 4, 5]])

b = a.unsqueeze(1) #再第1维上再进行扩展 (第0维到第1维) 维度（2，1，3）
tensor([[[1, 2, 3]],
        [[3, 4, 5]]])

c = b.squeeze(1) #去维度 在第1维度上降维 维度(2, 1) 注意只有第1维是多余的 可以降维
tensor([[1, 2, 3],
        [3, 4, 5]])


#torch.view() 相当于reshape改变tensor的维度 
a = torch.Tensor(2,3)
a.view(-1) #可以去除多余的维度
print(a)
# tensor([[0.0000, 0.0000, 0.0000],
#        [0.0000, 0.0000, 0.0000]])
 
print(a.view(1,-1))
# tensor([[0.0000, 0.0000, 0.0000, 0.0000, 0.0000, 0.0000]])

```

## 19 tensor各类数据转换

```
a = torch.tensor([1, 2])
b = a.tolist() #转换成列表

```

## 20 采样器

    torch.utils.data.sampler
    
采样器会将数据集成到一块，需要输出时才会进行相应的操作。
比如SubsetRandomSampler，只有在遍历输出时采用随机打乱顺序，且每次输出的结果不一致。


