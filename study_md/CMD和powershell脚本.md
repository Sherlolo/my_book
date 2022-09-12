# CMD和powershell脚本

标签（空格分隔）： study

---

# CMD脚本

CMD命令行也指dos shell，是win10前主要的shell。
其脚本文件后缀位.bat


## 基础语法

常见基础语法如下：

- :: 表示注释
- echo 显示输出
- pause 暂停等待输入任一字符


```
@echo off :: 关闭自身命令的提示
python myScript.py
pause
```

## 常用系统变量

```
%CD%  获取当前目录
%PATH%  获取命令搜索路径
%DATE%  获取当前日期。
%TIME%  获取当前时间。
%RANDOM% 获取 0 和 32767 之间的任意十进制数字。
%ERRORLEVEL% 获取上一命令执行结果码
```

## 变量设置

```
SET variable=[string] #variable表示变量名，string表示变量值。=的左右不要有空格

set str=superhero
echo %var%
```

# powershell脚本

powershell脚本和c语言一样，其语法不区分空格等空字符。
和sh脚本语法类似

## powershell版本

powershell脚本文件的后缀是ps1、ps2。后面的数字代表版本号，一般默认是用ps1。
因为ps2的兼容性好，而且能满足大多数的应用场景

>可以使用 Get-Host 获取当前的ps版本信息 #代表注释

## 变量

利用$创建变量

```
$array = 1,2,3,4    #创建变量
$array[1] #访问
```

## 运算符

### 条件判断

```
-eq ：等于
-ne ：不等于
-gt ：大于
-ge ：大于等于
-lt ：小于
-le ：小于等于
-contains ：包含
```

### 逻辑运算符

```
!($a): 求反
-and ：和
-or ：或
-xor ：异或
-not ：逆
```

### 特殊的运算符

```
-split: 将一个字符串分为几个部分
-join: 将几个字符串合成一个字符串
'A B C DE' -split ' '
'A','B','C' -join ','


-is: 判断类型
-isnot：判断不是某种类型
[] : 运算符用于转换变量的类型
[Float]$pi = 3.14
```


## 语句

### if判断

```
$condition = $true

if ($condition -eq $true) {
    Write-Output "condition is $true"
}
elseif ($condition -ne $true ) {
    Write-Output "condition is $false"
}
else {
    Write-Output "other ocndition"
}
```


### switch判断

```
$n = 4
switch ($n) {
    1 {"n is 1"}
    2 {"n is 2"}
    3 {"n is 3"}
    default {"n is others"}
}
```

### 循环语句

```
$i = 0
do {
    $i++
    Write-Output $i
}while ($i -ne 3)


$i = 0
do {
    $i++
    Write-Output $i
}until ($i -eq 3)


$i = 0
while ($i -lt 3) {
    Write-Output $i
    $i++
}


for ($i = 0; $i -ne 3; $i++) {
    Write-Output $i
}


$array = @(1, 2, 3, 4)
foreach ($i in $array) {
    Write-Output $i
}
```

## 系统变量

```
$Args[0] #命令行传入的参数
```