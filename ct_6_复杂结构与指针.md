---
title: '6 复杂结构与指针'
date: 2020-10-29 19:51:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 结构体

* 关键字：struct
    * 声明结构体变量时，需要加上struct，而不能只用结构体名字
        * struct person Yu;
* 赋值、访问：
    1. '.' 直接引用—— '.' 前面是结构体变量
    2. '->' 间接引用——'->' 前面是指向结构体变量的指针
    * 举例：a. 自己回家、b. 告诉朋友自己家地址，来我家
* 声明结构体变量时的**占用空间**
    * 定义结构体不占用空间
    * 各变量的累加之和
    * 但要考虑**对齐原则**
        * 成员所占字节数的**最大值**的整数倍
        * 省空间做法：将同一类型的变量放在一起定义
    * 可以使用预定义的宏强行更改对齐的字节数
        * 参考[#pragma pack()用法](https://www.jianshu.com/p/d994731f658d)-简书
* 匿名结构体：struct {...;} var;
    * 只在初始化的时候使用一次
    * var就是声明的结构体变量了。**定义结构体同时声明变量**
## 共用体（联合体）

* 关键字：union
    * 共用一片空间
    * 空间大小按共用体内**占用内存最大**的字段来划分
* unsigned char的意义

    * <img src="https://i.loli.net/2020/11/17/PZuhzxBVLwHOeJT.png" alt="图片" style="zoom: 50%;" />

    * 首先我们通常意义上理解，byte没有什么符号位之说，更重要的是如果将byte的值赋给int，long等（而不是）数据类型时，系统会做一些额外的工作
    * 如果是**char**，那么系统认为**最高位是符号位**，而int可能是16或者32位，那么会对最高位进行扩展，赋给unsigned int也一样
    * 而如果是unsigned char，那么不会扩展
* 内存占用结构图

*  <img src="https://i.loli.net/2020/11/17/7jWxi3TVcYErLtA.png" alt="图片" style="zoom: 50%;" />

## ⭐指针与地址

* 学会对数据的本源去操作
* 取地址符：&
    * 取的是首地址：第0个字节的地址
* **每一个字节都会对应一个地址标号**
* 64位/32位：地址的寻址范围
    * 32位最多匹配4GB的内存条
        * 2^30 Byte = 1 GB 👉 32位最多支持4 GB
* **指针变量**
    * 用来存储地址，地址一般表现为16进制
    * 不管指针什么类型（int、char），它们的地位一样，都是存储地址
        * 占用空间大小：64位-8字节；32位-4字节（64位代表地址的位数是64）
        * **int \*p可以传给char \*q吗**？完全可以，都是占用8个字节（64位）
        * 区分类型的意义见下：等价形式转换
    * int *p;
        * 定义的时候*用来表示p是指针变量
        * 定义完后，p代表指针，\*p的\*代表**取值**操作
    * 指针变量也是变量！
        * 可以用指针Q存储指针p的地址
* scanf 函数说明
    * 要修改变量的值，所以必须用**地址传值**
        * 传地址的参数，可以叫**传出参数**，因为操作也会在函数外体现
    * **而**函数的值传递，其操作只在作用域里生效
* 等价形式转换

    * <img src="https://i.loli.net/2020/11/17/LAiPEXNwhfdKkzR.png" alt="图片" style="zoom: 50%;" />

1. 取值操作
2. +1 的1的偏移量取决于**p表示的类型的字节数（区分类型的意义）**
3. 前提：指针p指向**结构体**变量a
## 函数指针

* 变量：为了避免歧义，函数变量声明应该是这样

    * <img src="https://i.loli.net/2020/11/17/ERfATwVZFMzcjeP.png" alt="图片" style="zoom:50%;" />
    * 将\*add（add为函数名）括起来

* 类型：typedef可以将变量提升为类型

    * <img src="https://i.loli.net/2020/11/17/J7snje1GcWitXM5.png" alt="图片" style="zoom:50%;" />
    * 可以用类型定义变量

* typedef的两种用法
    * 内建类型、结构体类型的重命名
        * typedef long long lint;
        * typedef char \* pcahr;  // char \* → pchar
        * 有点像#define，但还是有微妙的区别，**详见**代码演示
        * typedef struct __node { int x,y;} Node, \*PNode;
            * 同时定义了两个别名
            * PNode p;  // 可以定义Node结构体类型的指针变量
    * 提升为类型
        * typedef int (\*func)(int);
            * 可以定义无数多个函数指针变量
* main函数参数

    * <img src="https://i.loli.net/2020/11/17/P7dFioWcXpn5YGv.png" alt="图片" style="zoom:50%;" />

    * 带参数的话，传参数的是谁呢？操作系统
    * return 0;  // 返回给系统
    * "echo $?" 可以用来判断系统上次命令运行是否成功
        * 0：成功
        * 非0：不成功
    * **参数解读**
        * argc：参数个数
        * argv：对应二维数组，接收的是argc个字符串，一维数组接收的是一行字符串
            * \*arr[] 对应 arr[][]
        * env：环境变量
            * env是指向char\*的指针
                * env是一个地址，指向一个字符数组的地址
                * env这个地址还能往后走，所以实际对应一个二维字符数组
            * ❓\*\*env与\*env[]的定义方式有什么不同？在这里是没区别的！
        * ❓❗**不能用int \*\*表示二维数组**
            * 需要用malloc定义？
            * 因为静态数组的话好像无法保证连续性
            * 这里需要去网上**细品！**
            * 参考[为什么不能用二级指针直接指向二维数组](https://blog.csdn.net/yongheng_1999/article/details/52765130)-CSDN
>问：泽哥，我定义一个char str\[10][10]的二维数组，不能用char\*\*接收。而main函数的\*\*env可以接收字符串数组，我该怎么理解，字符串数组不算二维数组？
>答：后者是通过malloc出来的，是动态数组
# 随堂练习

## 一：结构体字节对齐

*  <img src="https://i.loli.net/2020/11/17/y92dEkh8jmf7FlR.png" alt="图片" style="zoom:67%;" />

* 定义顺序不一样
* 左：8字节；右：12字节
## 二：共用体内存图

*  <img src="https://i.loli.net/2020/11/17/UAwFbq9jCoYhmRa.png" alt="图片" style="zoom: 67%;" />    

👇

*  <img src="https://i.loli.net/2020/11/17/mMrBg5pWSayw6RV.png" alt="图片" style="zoom: 67%;" />

## 三：ip转整数

*  <img src="https://i.loli.net/2020/11/17/JQ2gq5ZDT1lRcI6.png" alt="图片" style="zoom: 67%;" />

* 192.168.1.2
    * 每一段，最高255，即可以用1字节来表示，对应unsigned char
    * 总共就是4字节，对应int类型
* **代码**
* <img src="https://i.loli.net/2020/11/17/f5bWAZchgJDGSrU.png" alt="图片" style="zoom: 67%;" />
  * union中struct必须声明变量
    * 可以不用sscanf？如果不用，则不方便读取xxx.xxx.xxx.xxx中每一段的值
    * 大端、小端机
        * 小→数字低位→低地址
        * 大→数字低位→高地址

|ip（192.168.0.1）按每段顺序存储在**小端机**里|    |    |    |    |
|:----:|:----|:----:|:---:|:----:|
|int数字**字节**地址编号|**0**|**1**|**2**|**3**|
|ip存储|1|0|168|192|

* 
    * 
        * 一般的电脑都是小端机
        * 判断是否是小端机：定义int类型num = 1，判断其最高位的值
            * int类型地址强转为char类型地址，则可以取第0号字节地址上的值
            * 直接读数字会按照本机字节序从高/低位开始读是吗？是的
        * 本端、对端字节序不匹配怎么办
            * 过程：本机字节序→网络字节序（统一标准）→对端字节序
            * 只有读取的时候，才必须区分字节序，其他情况都不用考虑
                * 如果没有细化到字节，应该是体会不到字节序的
            * 参考[理解字节序](https://www.ruanyifeng.com/blog/2016/11/byte-order.html)-阮一峰
    * 输出

*  <img src="https://i.loli.net/2020/11/17/zXudAFboiLDrCc6.png" alt="图片" style="zoom:80%;" />

## 附加：用尽可能多的形式表示a[1].x

*  <img src="https://i.loli.net/2020/11/17/lzCgMVIp7kjSPAy.png" alt="图片" style="zoom:67%;" />

【至少有30种，利用前面提到的等价形式、套娃！】

*  <img src="https://i.loli.net/2020/11/17/LAiPEXNwhfdKkzR.png" alt="图片" style="zoom:50%;" />

* **代码**

*  <img src="https://i.loli.net/2020/11/17/m6u8hBE4evYtSIU.png" alt="图片" style="zoom:67%;" />

## 亮点笔记

# 代码演示

## **代码**

### **演示结构偏移量计算、宏定义类型和typedef区别、主函数参数详解**

*  <img src="https://i.loli.net/2020/11/17/xOaSGdHoXPIRmuY.png" alt="图片" style="zoom:67%;" />

*  <img src="https://i.loli.net/2020/11/17/J3gtl9a2eDyoWIV.png" alt="图片" style="zoom: 67%;" />

* 字符串要以'\0'结尾，才方便结束读取的判断（num的低位为0即可）
* 地址对应的是long int类型（64位-8字节，32位-4字节）
* 被注释掉的offset()宏定义有什么不足？
    * 还需要定义变量，会占用空间
    * 所以可以借用空地址（0或NULL）强转
* 定义类型别名时，宏定义只是简单替换，没有tpydef省心
* main函数输入参数
    * 根据空格划分，可以用*双引号*整合多个字符串
* 程序输出

*  <img src="https://i.loli.net/2020/11/17/ebmf9VMDQCvIohB.png" alt="图片" style="zoom:67%;" />


# 附加知识点

* 在C++的面向对象中，结构体是一个class
* 引用效率比指针高，指针多了一个传递
* 如何根据一个已知的内存地址，读取值
    * 将地址传给一个指针，再用取值符*
* 语法糖
    * 指计算机语言中添加的某种语法，对语言的功能没有影响，但是更方便程序员使用
    * 让程序更加简洁，有更高的可读性
# 思考点

# Tips

* 重点在于指针与地址的应用关系
* 记笔记，很重要
  
    * typora记录
* 简历
    * 可以用[ShareLaTeX](https://cn.sharelatex.com/)做
    * 计算机方向的简历：黑白，注意缩进，感觉踏实
* 参考工具书第9、10章

---


# 课程速记

