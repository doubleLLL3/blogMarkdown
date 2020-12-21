---
title: '1 语言入门基础'
date: 2020-10-13 18:12:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## C语言概述
* 为什么要学C语言？

|**汇编语言**|**C语言（编译型语言、高级语言）**|
|:----:|:----:|
|强依赖于硬件设备-寄存器|可移植性|
|直接与寄存器等沟通，代码复杂，效率更高|简单、可读、开发效率高|
|举例：想与外国人交流| |
|学英语|找翻译|

* C的重要性
    * 先有C，再有Linux
        * 操作系统Unix收费，所以有Linux，李纳斯开发的
        * Linux底层是用C语言实现的
    * Java、C++、Python底层都是C
    PS：Mac OS是基于Unix的

* 学习思维逻辑、编程范式
    * 而不仅仅是语法
    * C语言只支持一种：面向过程
    * C++支持四种：面向过程、面向对象、泛型编程、函数式编程

## 输入与输出函数
类比人的听、说
* 输出函数：printf

|头文件|stdio.h|    |
|:----|:----|:----|
|**⭐原型**|int printf(const char *format, ...);|输出的都是**字符**|
|    |**返回值**，int，成功**输出的****字符数**|'\n'是一个字符|
|    |参数1，format，格式控制字符串|    |
|    |可变参数列表，**...**|**合法语句**，可以通过编译|

* 输入函数：scanf

|头文件|stdio.h|    |
|:----|:----|:----|
|**⭐原型**|int scanf(const char *format, ...);|    |
|    |返回值，int，成功**读入的****参数个数**|**<0，不合法；≥0，合法（包括0）**|
|    |while(scanf(...) != EOF)|-1 <=> EOF （隐藏的文件描述符）|

* 键盘输入文件结束符EOF：
    * 1. Unix：Ctrl+D
    * 2. Windows：Ctrl+Z
* 也可以使用Ctrl+C中断循环 

## C语言文档与编码规范

* 利用维基百科查看函数
* cpp reference 权威文档
    * 可参考[海贼宝藏](https://www.haizeix.com/)官网
* 编码规范（more code, more bug）**谷歌**是爸爸
    * 百度+谷歌
    * 阿里+谷歌
    * 降低bug率
* 工具书（**1个月**足矣；该书为跨界编写）

    * <img src="https://i.loli.net/2020/11/16/hPGH9pzquksE1Dx.png" alt="图片" style="zoom: 33%;" />

# 随堂练习

*  <img src="https://i.loli.net/2020/11/16/qDJH9LtToBEUAQi.png" alt="图片" style="zoom: 50%;" />
   *  不用while函数，可以直接使用printf的返回值
   *  printf()嵌套
   *  **代码**

<img src="https://i.loli.net/2020/11/16/CAle6Wok8QSX1sL.png" alt="图片"  />

*  <img src="https://i.loli.net/2020/11/16/v6a5WsAIgPuBRYK.png" alt="图片" style="zoom: 80%;" />

1. %\[^\n]s

>// 目的：
>char str[100] ={0};
>scanf("%s", str);         // 遇到空格等分隔符就会进入下一个参数的赋值
>scanf("%\[^\n]s", str);  // 可以把空格当做字符读取，[]后的s可有可无

|[]|字符匹配集|
|:----:|:----|:----:|:----|
|^|取非/除了|
|\n|换行符|

2. 当循环读入时，会有问题

>while (scanf("%\[^\n]s", str) != EOF) {
>...
>}

* 输入Hello World，输出不会停止，一直循环输出Hello World

3. 因为\n被当做分隔符进入下一个参数的赋值，但\n不会被吞掉

* 参考开课吧QA--[10月13号C语言课程有关scanf的问题](https://xue.kaikeba.com/qa/?qa=523/1013cscanf&show=523#q523)：之所以会卡在'\n'是因为%[]不能吃掉前导空白符
	

**自己的理解思路，与上面有点出入**

* 将scanf的输出显示，可以看到只有第一次成功读入了1个参数，后面全是0

*  <img src="https://i.loli.net/2020/11/16/wdq5zMVJxtosilm.png" alt="图片" style="zoom: 67%;" />

* 按照我的理解，\n如果在正则里被【取非】了后，就无法把\n读进去了，但是会当成分隔符，所以进入输出语句，但一直读不进，所以又卡在那循环读入；
* 而没有正则的时候，\n被当做分隔符读入，且继续读取下一个字符，所以停止了，只是遇到空格就会进入到下一个参数的赋值。
* <img src="https://i.loli.net/2020/11/16/QLPdrzH6qe9nbt5.png" alt="图片" style="zoom: 67%;" />

4. 使用getchar()，可以吞掉一个字符，比如\n

	* 打印getchar()的值，可以发现吞掉的是\n，这样继续读取下一行了

*  <img src="https://i.loli.net/2020/11/16/lNSR7IPVh2HU1oZ.png" alt="图片" style="zoom: 50%;" />
*  红框为输出被吞掉的字符：\n，可见下图代码的15~20行

* 也许也可以通过循环读取char的方式来读取包含空格的字符串，使用二维字符数组，遇到\n就进入下一行。
* 另一个角度的思考，参考[scanf中的\n问题](https://blog.csdn.net/weixin_44967885/article/details/108994960)-CSDN
* **代码**

*  <img src="https://i.loli.net/2020/11/16/MEgCp8h4asQe7BH.png" alt="图片" style="zoom: 80%;" />

## 亮点笔记

* scanf利用**分隔符**（空格、换行等）来分隔几个变量输入
* 赋值运算符（=，-=等）的优先级很低（结合方向：右到左），低于==运算符
* 赋值运算符形成的表达式的值就是**所赋的值**
>int ret;
>printf("%d\n", ret = 3);
>// 输出3
* ./a.out**> output**可以将输出放在output文件里（覆盖原文件），而不显示在终端

    * \>的含义是标准输出重定向
* vim基本操作，可进行vimtutor练习，见石墨文档-[Linux下常用命令：Vim](https://shimo.im/docs/VMAPVmDmvQH2Dvqg#anchor-Bj9O)
* printf()的格式控制字符串中，如果不用\n，末尾有个%
  
    * 这个百分号是shell给你加的，因为你自己没有输入换行。
>​			想想如果你没输入换行的话，shell后面的那个提示符它是不是应该紧接着你的输出(如hello word)去写，显得乱
* 注意：%c慎用，什么都会读进去
# 代码演示

## printf函数

### printf家族基础知识

* printf：打印到标准输出（stdout）

![图片](https://i.loli.net/2020/11/16/ZaJBFcLsyUpzvVd.png)

* **spirnf**：打印到字符串（string）
    * 可用于字符串拼接
    * sprintf(str, "", ...);
* fprintf：打印到文件中（file）
    * 先打开文件，fopen：w写，r读，a追加
        * 返回FILE类型的指针
    * fprintf(FILE指针, "", ...);
    * “...”：读变参列表变量的地址
        * 字符串变量本身传的就是其首地址

<img src="https://i.loli.net/2020/11/16/z3T5hSQjeYNPIy9.png" alt="图片"  />

>int main()
>{
>	FILE* fp;
>	int i = 617;
>	char* s = "that is a good new";
>	fp = fopen("text.dat", "w");
>	fprintf(fp, "%d\n", i);	// 传变量名即可，不用传地址
>	fprintf(fp, "%s", s);
>	fclose(fp);	// 记得与fclose呼应，关闭文件
>	return 0;
>}
### 练习

1. 根据n的末尾三位二进制的值，给str分别加不同类型的括号

*  <img src="https://i.loli.net/2020/11/16/taPydwmEASQnf2M.png" alt="图片" style="zoom:80%;" />

* n的定义在printf开头部分
* 使用宏定义方式定义swap函数

![图片](https://i.loli.net/2020/11/16/w8xvPeogXGa765Q.png)

​		__typeof 获取变量类型，直接用来声明中间变量的类型

* sprintf的两个参数不能一样？
    * **💡名字不能一样**？
        * 如果前后参数都用str，见下：

*  <img src="https://i.loli.net/2020/11/16/CdOZpuAtceJihGV.png" alt="图片" style="zoom:80%;" />

​					结果如下：其中，()里的值长度与原值一样，输出的内容()**似乎会覆盖原来的内容**

*  <img src="https://i.loli.net/2020/11/16/OPM8qEsxG9bFU2Z.png" alt="图片" style="zoom:80%;" />

- 
  - 一定要用指针的方式传地址？
    - 如果直接使用变量，可以，见下：
    
- <img src="https://i.loli.net/2020/11/16/B4OxXsHLqVCwiAG.png" alt="图片" style="zoom:80%;" />

- 
  - 
    - 但是有多个条件判断，琢磨琢磨不用指针的话，**交换字符数组**好弄吗？？？
         * ❓其实字符数组的变量名代表的就是字符数组首地址，可以直接交换地址？失败告终，应该是不行的，毕竟数组怎么会轻易移动，但可以移动指向数组的指针
      * 所以还是学会用指针~
      * fprintf的FILE指针也可以是stdout和stderr

*  <img src="https://i.loli.net/2020/11/16/3pI1FCWrHO2RZLA.png" alt="图片" style="zoom:80%;" />

- 
  - 
    - 最后输出如下：

*  <img src="https://i.loli.net/2020/11/16/HKRgMXqbxrI96SB.png" alt="图片" style="zoom:80%;" />

- 
  - 
    - stdout和stderr有什么区别呢？
      - 可以使用>将**标准输出stdout**重定向，对于stderr不会重定向，见下：

*  <img src="https://i.loli.net/2020/11/16/pAtjCIUl51ukZQo.png" alt="图片" style="zoom:80%;" />

​							⭐：可以单独看error信息，而屏蔽掉大量的正常输出

- 
  - 
    - 
      - 同样，也可以使用**2>**只将错误输出重定向，见下：

*  <img src="https://i.loli.net/2020/11/16/WUpR2P7C6fd94xF.png" alt="图片" style="zoom:80%;" />

# 附加知识点

* 文件描述符
    * stdin：标准输入处
    * stdout：标准输出处
    * stderr：标准错误输出处
    * EOF：-1
* 文件权限
    * Linux中一切皆文件~
    * ls -al *.*：可显示文件权限
        * rwx 可读可写可执行
* [数组初始化的三种常用方法（{0}, memset, for循环赋值）](https://blog.csdn.net/slx_391987/article/details/39558227#)-CSDN
# 思考点

* 为什么scanf中参数列表要传地址？
    * scanf需要修改参数的值，如果传值的话，将无法改变其值
