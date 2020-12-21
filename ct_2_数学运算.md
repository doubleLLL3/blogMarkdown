---
title: '2 数学运算'
date: 2020-10-15 13:51:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## C语言的数学运算

*  <img src="https://i.loli.net/2020/11/16/IPUscMn7lmZz5Yv.png" alt="图片" style="zoom: 80%;" />

* = 赋值
    * 可以与和其他运算符结合使用：+=、<<=、...
* / 除以
    1. 左右都是整数：整除，下取整
    2. 左右至少有一个浮点数：得到的才是浮点数
* () 提高运算优先级
* **⭐位运算**，效率高
    * 计算机对于不同的数据类型，都统一按**二进制数据**存储，即按bit位存储
    * 8bit→1byte
    * int类型是32位bit，对于有符号的最大能表示2^31-1
    * 位运算时，①先展开成二进制位表示，②只有对应的位之间有关系
    * 位运算不支持浮点数？
        * 没有意义。对于64位的浮点数，最高的1位是符号位S，接着的11位是指数E，剩下的52位为有效数字M。
        * 如果按位与，前面12位运算规则是什么呢？即指数位相与结果的意义是什么呢？这就是本质了，两个浮点数相与没有数学意义啊。
    * ^ 异或
        * 特点：是**自己的**逆运算；支持交换律、结合律
        * 逆运算
            * 前提：符合交换律
                * 减法、除法不符合
                * 减法是加法的逆运算，反之不是
                * 除法是乘法的逆运算，反之不是
* << 左移 >> 右移
    * 左移一位，乘以2
    * 右移一位，除以2，下取整，符号不变
        * 左边一直补符号位（最高位为符号位的有符号数）
        * 左边强制补0（无符号数）
## C语言的数学函数

头文件：math.h

**注**：abs()函数在stdlib.h里

*  <img src="https://i.loli.net/2020/11/16/d6B59PQ8oxr24W7.png" alt="图片" style="zoom: 67%;" />

|**原型**|double pow(double a, double b);|指数函数|
|:---:|:----|:----|
| - |double sqrt(double x);|开平方|
| - |double ceil(double x);|**上取整**|
| - |返回的还是double，因为double能够表示超出int的范围的数|    |
| - |double floor(double x);|下取整|
| - |int abs(int x);|**整数**绝对值；**stdlib.h**|
| - |**double**fabs(double x);|**实数**绝对值；f也许在以前代表float|
| - |double log(double x);|**以e为底的对数**|
| - |double log10(double x);|以10为底|
| - |**换底公式**可以求以**任何数**为底的对数：log(2)8 = log(8) / log(2)|    |
| - |double acos(double x);|**反余弦函数：arccos()**|
|    |    |返回值：角度的弧度值<br>**⭐π = acos(-1)**|

![图片](https://i.loli.net/2020/11/16/FV7M8exlYNhpK4c.png)

# 随堂练习

* <img src="https://i.loli.net/2020/11/16/DsqmB9QE3abPHG7.png" alt="图片" style="zoom: 80%;" />
* double类型的控制字符是“%lf”
* **scanf**的控制字符**必须**与参数的类型对应，否则读不进
    * double x;
    * scanf时一定要用“%lf”对应，不能用“%f”，否则如下：
      
        * <img src="https://i.loli.net/2020/11/18/DLjhJnli6qsXwHF.png" alt="图片" style="zoom: 67%;" />
        * printf没有它严格，控制字符可以不和类型完全一样，应该是有类型转换
        * **代码**
            * <img src="https://i.loli.net/2020/11/16/IQY4EPrnXOUNyma.png" alt="img" style="zoom: 80%;" />
          * 巧妙地是用pow，三分之一次方表示立方根
            * ⭐1/  3**.0**而不可以是 1 / 3
          * gcc编译时要添加math库，加-lm
* <img src="https://i.loli.net/2020/11/16/Fe26spVnGK9t8CM.png" alt="图片" style="zoom:80%;" />
* 注意π可以用acos(-1)得到更精确的表示
* **代码**
    * ![图片](https://i.loli.net/2020/11/16/WP25Xs4CKk6Go9R.png)
    * 先除后乘更安全，不容易溢出

## 亮点笔记

* double类型的控制字符是“%lf”
* π可以用acos(-1)得到更精确的表示
# 代码演示

## 练习

*  <img src="https://i.loli.net/2020/11/16/VThbE4Nxduk7JaQ.png" alt="图片" style="zoom:80%;" />

* 用异或做交换思路清奇，但其实并不推荐
    * 只能用于整型，才能异或
    * 速度并不比使用temp快
    * 同一个变量之间swap就会变成0：swap(a, a)
# 附加知识点

* [运算符优先级+结合顺序表](https://baike.baidu.com/item/%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BC%98%E5%85%88%E7%BA%A7)-百度
    * *p--的运算顺序是 ①p指向的地址-1，②再用*取该地址的值
        * (*p)--才是①先取p指向地址的变量值，再将变量的值-1
* 异或规律
    * a ^ a = 0;
    * 0 ^ a = a;
    * 可以在一个数字均出现两次的数组中找有且一个只出现一次的数字
* xshell运行C语言不报warning
    * g++ *.cpp -Wall
        * 使用该选项，允许发出GCC能够提供的所有有用的警告
        * 也可以用-W{warning}来标记指定的警告。
        * <img src="https://i.loli.net/2020/11/16/QCYwS8ZOzilycqf.png" alt="图片" style="zoom:80%;" />
# 思考点

* 用异或运算进行变量交换有什么好处？
    * 节约第三方变量的内存
    * 原理思考
        * 逆运算即可
        
    * ***或者***
    * 自身与自身异或为0
           * 任何变量和0异或值不变
    * 按上面的原理思考，其实也可以用+-运算来做？
        * a' = a + b
        * b = a' - b = a
        * a = a' - b = a' - a = b
        * 但是这样做并没有用到**位运算的高效性**，而且要保证**两个数的和不溢出**~
# Tips

* [C语言文档中文版](https://wiki.jikexueyuan.com/project/c/)-极客学院翻译
    * C语言大约有29个函数库
* 参考工具书章节

*  <img src="https://i.loli.net/2020/11/16/5Mu6ymavgbDqfFl.png" alt="图片" style="zoom:80%;" />

