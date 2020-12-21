---
title: '7 工程项目开发'
date: 2020-10-31 19:51:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 函数的声明与定义

* 声明：告诉系统有这个东西
  
    * 传入参数的变量名是什么不重要，此时不需要标明
* 定义：具体是怎么实现的
* 之前函数声明和定义都是同时进行的
* 编译顺序：从上到下、从左到右

    * <img src="https://i.loli.net/2020/11/17/1LbZyts8WpKg2Sx.png" alt="图片" style="zoom: 67%;" />

    * 上：gcc报错信息；下：g++报错信息（也许g++报错更友好）
    * 看报错的时候**从上往下**看，后面的错误可能就是由第一个错误产生的连锁反应
* 函数未声明和未定义暴露在**两个时期**
    * 函数未声明错误——编译过程（主要是语法检查）
        * *g++ -c  \*.cpp*生成编译后的对象文件
        * <img src="https://i.loli.net/2020/11/17/TMftSsLhFv2oYDw.png" alt="图片" style="zoom: 67%;" />
        * 
    * 函数未定义错误——链接过程
        * *g++  \*.o*链接生成可执行程序

        * <img src="https://i.loli.net/2020/11/17/wxeU25tJnAiKcZy.png" alt="图片" style="zoom:67%;" />
        * 
    * 上面报错信息来自于船长的clang编译器，我们使用的是g++编译器，显示有所不同
* 函数声明可以多次，但定义只能一次！
## 头文件与源文件

* **规范**
    * 头文件放声明，源文件放定义
        * 不应该都放在头文件里
    * 头文件与对应的源文件名称一致
* 头文件中条件式编译，可以避免**一次**编译过程中**重复包含头文件**的问题
```objectivec
#ifndef _HEADER1_  // 名称最好与头文件名对应，虽没有硬性要求
#define _HEADER1_
...
#else              // 可以没有
#endif             // 必须有
```
## 工程开发规范与静态链接库

* 可以将#include后的双引号""改成尖角号<>吗？
    * 双引号""：从**执行代码所在目录下**搜索
    * 尖角号<>：从**系统库路径**下搜索
    * 使用g++/gcc -I添加头文件路径到系统库路径下
* 向上开发时
    * 给别人**头文件**\(include文件夹\)、源文件对应的**对象文件的包**\(lib文件夹\)即可
    * 对象文件打包
        * 静态链接库（.a）
>// 打包
>ar -r lib**xxx**.a header1.o header2.o header3.o
>// 链接 g++ \*.o -L -l
>g++ test.o -L./lib -l**xxx**
>**// xxx对应**
* 
    * 
        * 动态链接库（.so）
        * 两者实现的功能一致，都是打包
        * [静态库与动态库的区别](https://www.nowcoder.com/questionTerminal/d16d0fe0e49448f5a24d10de08b27d53)-牛客讨论
## makefile工具

* 文档编译工具，定位类似markdown
* 封装编译流程，减小程序开发时编译的复杂度
* 示例

    * <img src="https://i.loli.net/2020/11/17/juTPX1lFMtcgA64.png" alt="图片" style="zoom: 80%;" />

    * .PHONY开一个虚拟的环境，避免使用make clean时与路径中存在的clean文件冲突
    * 可以有封装变量替换操作
## 初识谷歌测试框架

* 单元测试
    * 又称模块测试，是对程序模块（软件设计的**最小单位**）来进行正确性检验的测试工作
        * 在过程化编程中，一个单元就是单个程序、函数、过程等
    * 框架跟着语言走：C++、Python、Java...
* C++实现的
* cmake工具
    * 可根据本机的**环境**生成makefile文件
    * 为什么不直接使用makefile呢？makefile对环境的要求很强
    * 谷歌测试框架先cmake再make即可完成编译，注意打包库的位置
* **代码（main.cpp）**

    * <img src="https://i.loli.net/2020/11/17/FfdEBxbuHphLRjs.png" alt="图片" style="zoom: 67%;" />

    * 用的尖括号<>包含的gtest.h头文件
    * add2只是一个标识
    * 断言是什么？
        * 用来捕捉程序员自己的错误，假设某情况发生，如果没发生则作相应处理，
        * ASSERT_*版本的断言失败时会产生致命失败，并结束当前函数
        * EXPECT_*版本的断言产生非致命失败，而不会中止当前函数
* makefile

    * <img src="https://i.loli.net/2020/11/17/EXMxZaJQHUYORDP.png" alt="图片" style="zoom:80%;" />

    * 可以用 -std=xxx 指定C++版标准，本机其实不需要指定
    * 需要使用-I 添加头文件路径./lib
    * 使用-lpthread额外连接pthread库，mac系统会自动连接
    * 🆗疑问
    
        * <img src="https://i.loli.net/2020/11/17/GSgNa9hce4ETYD7.png" alt="图片" style="zoom:67%;" />
        * ①根据编译器支持的不同标准，c++11是比较低的版本了，要向下支持
        * ②可能用了make install之类的操作，将头文件包含到系统库目录里了
* 结果

*  <img src="https://i.loli.net/2020/11/17/xdba3vH19peQz4w.png" alt="图片" style="zoom:67%;" />

## ⭐实现自己的测试框架

* C实现的
* 需要实现以下三个函数或宏

|    |**TEST**|**EXPECT_EQ**|**RUN_ALL_TEST**|
|:----:|:---:|:----:|:---:|
|**功能**|代表一个测试用例|测试用例里的测试点|运行所有TEST|
|**宏/函数**|宏|函数或宏|函数或宏|
|**注意点**|没有返回值类型；<br>与后面的花括号{}组成合法的<u>函数定义</u>的形式|一种断言|返回值是0|

### 版本一：通过编译，显示测试结果

* haizei/test.h

    * <img src="https://i.loli.net/2020/11/17/aoDr8iP1xgQEl9W.png" alt="图片" style="zoom:67%;" />

    * 无法使用a##.##b
        * 函数名中只能是下划线、字母和数字，不能有"."！
    * a##_haizei_##b
        * 使用_haizei_或之类的特殊标识是为了防止a与b直接连接产生函数重名情况
        * 如(test, funcadd)与(testfunc, add)
    * ⭐__attribute__((constructor))
        * **设置函数属性**，在函数声明或定义时使用
        * 让它后面的**第一个函数**在主函数执行前被自动**调用**执行
        * 否则在[main.cpp](https://shimo.im/docs/NJkbE0p7WzuKm2qR#anchor-FxUW)中执行RUN_ALL_TESTS()后直接结束程序，不会经过TEST
        * 参考[函数属性__attribute__((constructor))和__attribute__((destructor))](https://blog.csdn.net/tianmohust/article/details/45310349)-CSDN
* haizei/test.cc

    * <img src="https://i.loli.net/2020/11/17/sZ1guoldtecF9KQ.png" alt="图片" style="zoom:80%;" />

    * 只需要象征性定义一下，通过编译即可
* main.cpp

    * <img src="https://i.loli.net/2020/11/17/Jq5c4mnb3xldYrC.png" alt="图片" style="zoom:67%;" />

    * 三组TEST
* makefile

    * <img src="https://i.loli.net/2020/11/17/lXYmz413Sify67T.png" alt="图片" style="zoom:80%;" />

    * 使用make可以快速编译
    * 注意-o的使用，让对象文件、可执行程序自定义命名放在指定目录下
    * 注意路径中文件所在文件夹的指明
* 测试结果

    * <img src="https://i.loli.net/2020/11/17/xgBjvpNUMmYXeGr.png" alt="图片" style="zoom:67%;" />
* ❓当前版本下，main()函数里return的无论是RUN_ALL_TESTS()还是0都会显示测试结果，如何让RUN_ALL_TESTS()控制显示的输出与否呢？

### 版本二：RUN_ALL_TESTS()开关

* 实现框架初衷——开关控制
* 需记录的点
    * 有多少组测试用例
    * 测试用例对应的函数名字
    * 测试用例对应的函数
        * 使用函数指针变量
        * 用数组记录函数指针
* haizei/test.h

    * <img src="https://i.loli.net/2020/11/17/dQIPSRG3UnvAVKN.png" alt="图片" style="zoom:67%;" />

    * TEST中，在main函数执行前使用add_function将函数记录到全局变量里
    * typedef的第二种用法：将变量提升为类型
    * **结构体的使用：封装函数指针和函数名**
* haizei/test.cc

    * <img src="https://i.loli.net/2020/11/17/l8GjynOH1Udu3Eq.png" alt="图片" style="zoom:67%;" />

    * 全局变量的使用
    * strdup的使用：参考[C语言strdup()函数：复制字符串](http://c.biancheng.net/cpp/html/166.html)

>使用malloc()开辟空间拷贝字符串，返回其地址，即字符串指针；
>最后记得使用free()释放
* 
    * main.cpp与makefile不变
    * ❗已经实现了开关控制，下面可以对显示、断言等进行优化！

### 版本三：人性化优化

#### ① 给输出添加颜色

* 参考[带颜色的printf](http://moilk.org/blog/2016/05/21/linuc01/)-Blog
* 将颜色定义封装成宏，在头文件haizei/test.h里定义

    * <img src="https://i.loli.net/2020/11/17/7LBR3ehnHkozFQG.png" alt="图片" style="zoom:67%;" />

    * COLOR 正常
    * COLOR_HL 高亮
    * COLOR_UL 下划线
    * 多个字符串之间用空格即可连接
    * 注意！颜色控制字符中 ";" 左右不能有空格
>正确：                         "\033[1;""31" "m" "%s\n"
>设置无效，啥都没有："\033[1; ""31" "m" "%s\n"
#### ② 添加断言宏

* 判断不等于、大于、大于等于、小于、小于等于
* 就事论事型：对每个宏单独实现
* 统一管理型：类似定义颜色宏，把共同的代码再封装一次

    * <img src="https://i.loli.net/2020/11/17/jJuW2NMDfZlK8L4.png" alt="图片" style="zoom:67%;" />
    * 掌握#的使用

#### ③ 统计每组测试的成功和失败的测试点个数，并显示

* haizei/test.h

    * <img src="https://i.loli.net/2020/11/17/8AZ9gyKpQBefnMj.png" alt="图片" style="zoom:80%;" />

    * <img src="https://i.loli.net/2020/11/17/uq4XoiRBfUSHeEZ.png" alt="图片" style="zoom: 67%;" />

    * 定义统计的结构体，统一管理，封装性更好
    * 在断言处进行统计
    * **这里使用extern声明结构体变量**，因为
        * 头文件的断言处用到了该变量，需要有该变量的声明
>int i 既是声明也是定义，extern int i只是声明
>struct FunctionInfo haizei_test_info 既是声明也是定义
>只是声明需在前面加extern
* 
    * 
        * 但在头文件不能定义变量，否则易导致**重定义**问题
        * 参考[C语言正确使用extern关键字](https://blog.csdn.net/xingjiarong/article/details/47656339)-CSDN
* haizei/test.cc
    * <img src="https://i.loli.net/2020/11/17/NjDZQTSgPKqzxhX.png" alt="图片" style="zoom: 67%;" />
    * 定义并声明haizei_test_info变量
    * 1.0提升类型，100.0放前面可能会溢出
    * 100%情况判断：用一个极小值和fabs进行浮点数判等；成功数==统计数
    * 居中对齐效果
        * %m.nf：输出共占m列，其中有n位小数，如数值宽度小于m左端补空格
        * %-m.nf：输出共占n列，其中有n位小数，如数值宽度小于m右端补空格

#### ④ ⭐显示失败测试点的详细信息

* 主要编写头文件中，断言宏中要执行的LOG宏

* haizei/test.h
    * <img src="https://i.loli.net/2020/11/17/NiMb58InopmOdhF.png" alt="图片" style="zoom:67%;" />
    * ⭐actual部分的结果值类型不确定，定义泛型宏
        * _Generic(a, 替换规则)：根据a的返回类型实现对应的替换
        * **_Generic是C语言中的关键字，不是宏**！在预处理阶段不会替换成对应类型
            * ① 在与COLOR宏连用时要十分小心！
                * 在编译阶段，字符串与一个不知道是什么的东西(_Generic())无法拼接
            * ② 不能使用C++编译器
            * ❗ 详见下面的**报错一**和报错二
        * 参考[cpp_reference](https://en.cppreference.com/w/c/language/generic)
    * 使用typeof定义额外变量
        * 所有运算部分通过额外变量，避免**\+\+操作**带来的多次运算
    * **报错一**（编译阶段 -c）
        * <img src="https://i.loli.net/2020/11/17/p9BsgAKfeVnX8PN.png" alt="图片" style="zoom:67%;" />
        * 对应错误写法：把TYPE(a)写在YELLOW_HL宏里
            * <img src="https://i.loli.net/2020/11/17/kYtVLfRDNnw1cz8.png" alt="图片" style="zoom:67%;" />
            * 红框②可以正常输出，但是会没有颜色
            * 如果像红框①那样在外层套一个颜色宏，编译会报错
                * 对main.c进行预处理，不会报错
                * 查看上面红框②预处理后的代码，如下
                * <img src="https://i.loli.net/2020/11/17/kGfo3P961mvRBEb.png" alt="图片" style="zoom:80%;" />
                * 
            * **原因**：对于宏替换后的代码，("字符串" _Generic() "字符串")在编译时报错，连接不上，因为编译器此时并不知道_Generic()是啥
                * _Generic()需要在运行时才能知道结果，语法检查时字符串和莫名的东西连接，所以报错
                * 和printf()的原型的第一个输入参数类型为const char\*没有很大关系，但是类型不匹配会报警告
                * 看下面这个简单的例子也许就明白了：
                    * 头文件
                    * <img src="https://i.loli.net/2020/11/17/3xyWdCbtIAJu9gU.png" alt="图片" style="zoom:80%;" />
                    * 源文件
                    * <img src="https://i.loli.net/2020/11/17/fGmUD6EAiClzJ7I.png" alt="图片" style="zoom:80%;" />
                    * 编译
                    * <img src="https://i.loli.net/2020/11/17/RKu9fZnG4gxaF7J.png" alt="图片" style="zoom:67%;" />
                    * 同样的报错
                    * 因为在编译检查语法阶段，编译器不知道s是个啥，和字符串"a"连接就会出错
                    * 报错提示的是想让你把s踢出去，直接在s前面加括号
        * 所以通过**sprintf**()将_Generic()包装起来的方式很巧妙，在编译阶段没有问题，在运行阶段有值了自然也正常
    * **报错二**（编译阶段 -c）
        * <img src="https://i.loli.net/2020/11/17/JEZbioezlvIsP2F.png" alt="图片" style="zoom:67%;" />
        * <img src="https://i.loli.net/2020/11/17/yngQlcpfmjswK2I.png" alt="图片" style="zoom:67%;" />
        * 关键信息在第二张图的error
>error: '_Generic' was not declared in this scope
* 

    * 
        * _Generic只支持C语言（C11），不支持C++
            * 参考[如何启用_Generic关键字](https://www.thinbug.com/q/28253867)-ThinBug
        * 将所有文件后缀改为C语言的
            * main.cpp → main.c；test.cc→test.c
        * 修改makefile，见后

* main.c

    * <img src="https://i.loli.net/2020/11/17/52gp9lAJOBMSEvx.png" alt="图片" style="zoom: 67%;" />
    * 测试double类型数据，验证泛型宏作用
        * 修改了函数的参数类型为double
        * 其实double判等不能直接用==，在头文件里要判等方式，用差值与极小值

* makefile

    * <img src="https://i.loli.net/2020/11/17/eZPRaXN3FIifpys.png" alt="图片" style="zoom:67%;" />

    * 

* 输出

    * <img src="https://i.loli.net/2020/11/17/4bIZkDOvGHLazpt.png" alt="图片" style="zoom:67%;" />

#### ⑤ 存储函数的全局变量没有测试用例数量限制

* 静态数组：在运行之前就开辟好了固定的空间大小，且存储的物理空间连续
* 链表：思维上是顺序的，但在物理存储上不需要顺序
    * 由节点组成，包含：数据域、指针域
    * 占用空间动态变化
    * 但是更厉害是的是下面的方式：可以给**任何结构体安上链表的外骨骼**
* ⭐⭐**链表外骨骼**
    * haizei/test.h
        * <img src="https://i.loli.net/2020/11/17/LK8kImz95rCPQOv.png" alt="图片" style="zoom: 67%;" />
        * 直接在一个结构体中添加节点结构体变量node，即链表结构的外骨骼
        * node记录下一个节点（下一个TEST的node）的地址
        * 包含链表节点的头文件haizei/linklist.h
    * haizei/linklist.h
        * <img src="https://i.loli.net/2020/11/17/ucTPZQ6mIhn31S4.png" alt="图片" style="zoom:67%;" />
        * next指向下一个节点的地址
            * 但实际是想访问下一个TEST的func和str字段
            * 可以通过**访问下一个结构体的首地址再间接访问两个字段**来完成
        * 如何得到一个结构体的首地址
            * 通过指针p对应字段name在结构体T中的偏移量来计算
            * **offset宏！**
                * 用空指针得到name字段所在的地址
                    * (T *)(NULL)->name得到的是name变量
                    * &得到的是T *类型指针，存的是地址
                * 转long整型即可得到偏移量
                    * **long**类型会根据系统位数改变其范围，对应指针大小
            * **Head宏！**
                * 将p指针的地址转成**char \***类型
                * 这样±1是按照最小单位1字节来偏移的
                * p是一个指针，name是指针p在结构体T中对应的字段名
    * haizei/test.c
        * <img src="https://i.loli.net/2020/11/17/AJWhNZCcqfDGtxi.png" alt="图片" style="zoom:67%;" />
        * 尾插法，定义一个尾节点指针func_tail
        * 得到结构体首地址，利用->间接访问变量
        * malloc() 和calloc()的主要区别
            * 前者不能初始化所分配的内存空间，而后者默认将开辟的空间初始化为0
>// 在堆区动态地分配一块指定大小 size 的内存空间，用来存放数据
>void* malloc (size_t size);
>// 在堆区动态地分配 num 个长度为 size 的连续空间，并将每一个字节都初始化为 0
>void* calloc (size_t num, size_t size);
* 
    * 
        * 同样适用strdup，复制一份字符串在新开辟的空间上，并返回其地址
            * strdup中malloc开辟的空间很容易忘记释放：[危险的strdup函数](https://book.51cto.com/art/201311/419443.htm)
        * calloc、strdup的空间需要自己去free
            * <img src="https://i.loli.net/2020/11/17/5QlHUrLkhY1sF2C.png" alt="图片" style="zoom: 50%;" />
            * *上图供参考*
            * ①free calloc的func空间前先保存下一节点的地址
                * 利用p->next即可
            * ⭐②**从里到外**free结构体变量
                * func->str  strdup通过malloc出来的
                * func calloc出来的
            * ③free完让指针指向NULL，避免成为野指针
            * 释放strdup的func->str指向的空间时，需使用(void *)强转一下
                * 否则会有free const char*的警告，参考：[In C, why do some people cast the pointer before freeing it?](https://stackoverflow.com/questions/34019977/in-c-why-do-some-people-cast-the-pointer-before-freeing-it/34023762)-Stackoverflow
                * 正如上面链接中提到的，其实free const类型就很奇怪
            * free结构体要注意细节，详见后文思考点：free结构体的细节
            * 查看func里变量的地址
                * <img src="https://i.loli.net/2020/11/17/QThaBiWVX9NA7sD.png" alt="图片" style="zoom:67%;" />
                * <img src="https://i.loli.net/2020/11/17/DT152XJwPzudCox.png" alt="图片" style="zoom:67%;" />
                * 按8字节对齐
                * 打印func->str打印的是strdup出来的地址，打印&(func->str) 打印的是结构体对象中成员str的地址

#### ⑥函数指针变量和函数名定义时的宏优化

* 方式一：宏替换优化 NAME、STR2
    * <img src="https://i.loli.net/2020/11/17/opRVAYOmaEG6yDU.png" alt="图片" style="zoom:67%;" />
* 方式二：宏嵌套 NAME、STR、_STR
    * STR(NAME(a, b, _))
    * 但是无法使用 '.' 连接生成函数名，可以使用 '_'
    * a##.##b在预处理阶段报错如下：
        * <img src="https://i.loli.net/2020/11/17/Mrbns4athuTDedQ.png" alt="图片" style="zoom:67%;" />
        * 把a.b作为参数、变量名是非法的→.有特殊含义
        * 参考[error: pasting “.” and “red” does not give a valid preprocessing token](https://stackoverflow.com/questions/13216423/error-pasting-and-red-does-not-give-a-valid-preprocessing-token)-StackOverflow
# 附加知识点

* 函数声明和主函数往上放，函数定义放后面，可以让代码框架、逻辑更清晰
* 简易工程文件结构规范
    * 使用tree工具

*  <img src="https://i.loli.net/2020/11/17/J3bAH2zrBwu4SKO.png" alt="图片" style="zoom:67%;" />

* make的规律
    * 如果makefile中有依赖文件做了修改
        * 直接make，相关文件就会自动重新编译，而可以不需要make clean做清理
    * 如果只是修改了makefile，而想重新生成对象文件
        * 一般要先make clean，再使用make重新生成新的对象文件，否则只是重新生成最顶层all的输出
* 可执行程序一般放在一个固定的目录下：**bin**
* 宏内注释
    * 单行宏：可以在后面直接使用 // 注释
    * 多行宏：只能使用 /\*...\*/ 注释
* 头文件只写函数的**声明**
* 宏嵌套宏
    * 当有#或##的时候，宏嵌套宏不能有效的展开，此时需要多加一层宏进行转换
        * 但只是#和##的地方停止了展开，其它地方会继续展开
    * 参考：[C/C++中的宏的使用技巧(宏嵌套/宏展开/可变参数宏)](https://blog.csdn.net/Pillary/article/details/53705158)-CSDN
* ⭐__attribute__((constructor))，详见实现自己的测试框架-版本一
* [C语言一行太长的换行处理](https://blog.csdn.net/baiqishijkh/article/details/79236202)-CSDN
* ❗ 宏定义中#的细节
    * 字符串化操作符
    * 作用：将宏定义中的传入参数名转换成用一对双引号括起来参数名字符串
    * 只能用于有传入参数的宏定义中，且必须置于**宏定义体中的参数名前**
* [\__typeof__()、__typeof()、typeof() 的区别](https://blog.csdn.net/Qsir/article/details/86496346)-CSDN
    * 推荐使用带下划线的
# 思考点

* 宏函数可不可以**重复定义**？
    * 函数定义放头文件里，不同文件多次编译会出现函数重复定义的情况
    * 而把定义成函数的宏扔在头文件里就没事？
        * 没事
        * 宏函数重复定义没问题，如下
    
            * <img src="https://i.loli.net/2020/11/17/2uHD4BU3gSayoGX.png" alt="图片" style="zoom:67%;" />
        
            * 对于这种情况，不要发生函数重名（a##_haizei\__##b）的情况
    * **但是**！宏不可以重定义，即不能修改之前的定义
* 宏定义不用考虑先后顺序！&&宏嵌套问题
    * 当调用了一个宏时，直接替换宏即可
    * 宏嵌套情况，参考[C语言宏替换的顺序](https://blog.csdn.net/feichideche/article/details/39521753)-CSDN
```c++
#define _ToStr(x) #x 
#define __ToStr(x) _ToStr(x) 
#define EarthQuake 9.0 
  
int main(){ 
    printf("%s\n", _ToStr(EarthQuake);   // EarthQuake
    printf("%s\n", __ToStr(EarthQuake);  // 9.0
    return 0;
} 
```
* 
    * 替换顺序
        * 从外层到里层，但遇到#或者##就会停止展开
        * 第一种：→#EarthQuake→"EarthQuake"
        * 第二种
            * 先替换第一层：→_ToStr(EarthQuake)→_ToStr(9.0)
            * 再替换第二层：_ToStr(9.0)→"9.0"
    * 嵌套定义：#define __ToStr(x) _ToStr(x)
    * 嵌套调用：__ToStr(EarthQuake)
* ❗free结构体的细节
    * free(p)只能释放指针p所在的内存，而p指向的内存（不是动态分配的）无法free
        * 参考[C语言free()函数：释放动态分配的内存空间](http://c.biancheng.net/cpp/html/135.html)
    * 所有动态分配的空间都需单独释放，从结构体**里到外**释放
        * 结构体在堆空间，而结构体内也有变量在堆空间，需要先free成员变量，最后free这个结构体
# Tips

* aka的中文意思是“又名”
* 不只是看船长秀，自己要思考怎么优化？怎么开发？怎么变成自己的知识点？
* 坚持不住，才要坚持，往往这是最有价值的
* 编译报错时，从上到下看报错信息，后面的报错也可能源于前面

---


# 课程速记


