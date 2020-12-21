---
title: '4 函数'
date: 2020-10-17 19:51:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 函数基础知识

* 函数封装
    * 可读性、美观性、⭐调试性（按函数模块debug即可）
* 函数声明三组成（必须）
    * 返回值
        * 无返回值写void
    * 函数名
    * 参数声明列表
        * 参数类型+参数名
* 函数定义
    * 花括号里的内容
## 递归函数

* 一类编程技巧（for循环、if语句、递归），而不是算法（递推）
* 函数自己调用自己
* 组成部分
    * 语义信息：根据需求设计
    * 边界条件：设计递归出口，可以有多个
    * **递推公式**：针对问题！
    * 结果返回：不只是通过return
        * 两种方式：return返回、传出参数（指针）
* 怎么证明递归函数是正确的？
    * 简单递归：将中间过程展开
        * 向下递推+向上回溯
    * 复杂递归：数学归纳法
        * 比如阶乘
            * fac(1)成立
            * 假设fac(k)正确
            * 证明fac(k+1)正确，即可
## 函数指针与应用

*  <img src="https://i.loli.net/2020/11/16/dfRvTkltpINMDWL.png" alt="图片" style="zoom:50%;" />

* 将函数名以指针的形式传入函数，在函数中通过函数名可以直接使用函数
  
    * 返回值 (*函数名)(参数类型列表)
* 应用
    * 实现分段函数，如上图
    * [欧拉题库](https://projecteuler.net/recent)PE-45<img src="https://i.loli.net/2020/11/16/LKIySsYUojd9k8F.png" alt="图片" style="zoom: 67%;" />
    * 找40755后，同时是三角形数、五边形数、六边形数的数字
      
        * 即找下一个三角形数，也满足五边形、六边形数字条件
    * 二分查找
        * 单调序列 上面的数字序列都是有序的
        * 时间效率：O(logn)
    * **代码**
    * <img src="https://i.loli.net/2020/11/16/GMwNeW1otX8bZcD.png" alt="图片" style="zoom:67%;" />

### **⭐该题亮点很多，主要注意6个关键**

1. 二分查找的不一定是数组，有**映射关系**的单调序列即可
   2. 根据序列特点调整了区间头、尾
3. **对偶逻辑**减少缩进，增加可读性
4. 三角形数包含六边形数：第2n-1个三角形数＝第n个六边形数
5. 如果用int类型（4字节），会陷入死循环
      1. int类型不足以涵盖下一个所求数字
      2. 用**long long int**类型（8字节），控制字符：%lld
6. 固定六边形数，跨度更大，速度更快，且根据第4点可知其一定是三角形数
* 其他
  * 应该可以用比temp更小的右边界或比1更大的左边界
      * 不过因为是O(logn)，所以缩小后效果提升不会很大
  * 这里用了函数指针，如果不用，得写多少个差不多的binary_search函数？
      * 不过也可以用宏去做函数的切换
  * 图中红叉处为笔误，Hexagonal修改为Triangle

## 欧几里得算法

又叫辗转相除法

* 快速计算最大公约数
    * 如果gcd(a, b) = gcd(b, a % b) = c，将使规模变小
    * **证明**

①设c为a、b的最大公约数，证明b、a % b也**存在**公约数c

>设正整数k1、k2、k3
>  a = k1 * c
>  b = k2 * c
>  a % b = a - k3 * b // 取模的本质
>所以a % b= k1 * c - k3 * k2 * c =(k1 - k3 * k2) * c
>即a % b是c的倍数，又b是c的倍数
>  故c是两者的公约数

②证明c是**最大**的公约数

*  <img src="https://i.loli.net/2020/11/16/GF17pRyE6ewiCYm.png" alt="图片" style="zoom: 67%;" />

>证明b、a % b的另两个约数k2、k1 - k3 * k2互质即可
>设gcd(k2, k1 - k3 * k2)= d (正整数)，即证d = 1
>设正整数m、n，可令
>  k2 = m * d
>  k1 - k3 * k2 = n * d
>则
>  k1 =n * d + k3 * k2 =(n + k3 * m) * d
>可得
>  gcd(k1, k2) >= d
>又因为
>  gcd(a, b) = c
>  a = k1 * c
>  b = k2 * c
>即
>  gcd(k1, k2) = 1
>所以
>  d = 1

* **代码**

*  <img src="https://i.loli.net/2020/11/16/k4fJGhw9DKoCvsN.png" alt="图片" style="zoom: 80%;" />

* 递归函数
    * 可用一句实现gcd函数
        * 将if语句换成问号表达式
* 不需要根据a、b大小交换顺序？不需要
    * 如果a ≥ b：gcd()函数按正常思维进行
    * 如果a < b：gcd()函数将交换两个变量
* Ctrl+D会重复上次输出而不是终止？
    * 【返回读入的字符数为0时会终止，比如输入非一个int值】
    * 因为没有对scanf返回值是EOF的判断
    * 可以在scanf前取反"~" 或 在后面做-1判断
## 扩展欧几里得算法

* 快速求解ax+by=1的一组整数解
    * gcd(a, b) = C的C为何值时，能求解一组整数解
        * C可取1或>1两种情况
>  a = nC
>  b = mC
>  nCx + mCy = 1
>  C(nx + my) = 1
>nx + my必定≥1
>C只能取1

* 在辗转相除法最后一轮只剩下1、0时，即互质时，就说明有整数解
* **数学归纳法**
    * 流程
        * 对于f(0)成立→假设f(k)成立→若可以得到f(k+1)也成立→则k为任意都可以成立
        
        * <img src="https://i.loli.net/2020/11/16/jO6ZS5yTqIYlxUW.png" alt="图片" style="zoom: 33%;" />
        * 参考上图、下表，向下递推+向上回溯

|    |**a**|**b**|**x**|**y**|
|:----:|:---:|:----:|:---:|:----:|
|**第k+1层（推导）**|a|b|y|x - ky|
|    |↓↓↓| ↓↓↓ |↑↑↑| ↑↑↑ |
|**第k层（假设）**|b|a % b|x|y|
|**...**|↓↓↓| ↓↓↓ |↑↑↑| ↑↑↑ |
|**第0层（成立）**|1|0|1|0（任意）|

**代码**

*  <img src="https://i.loli.net/2020/11/16/WJyTXIOVjDPh8oK.png" alt="图片" style="zoom:80%;" />

* 函数输入的地址一开始没有值，一直到最深处才赋值，再回溯

*  <img src="https://i.loli.net/2020/11/16/PtrEN3WwM4zI2nD.png" alt="图片" style="zoom:80%;" />

* 输出的等式右边不一定是1
    * 其实扩展欧几里得算法可以快速求解ax + by = gcd(a, b)的整数解
## 变参函数

*  <img src="https://i.loli.net/2020/11/16/51Pa62l3JRM7WIs.png" alt="图片" style="zoom: 80%;" />

通过上述场景来学习：

* 问题不是函数如何声明，而是该如何定位到变参列表的每一个参数
    * 不知道变参列表里变量的名字
    * 举例：老师想找张三同学后面一位同学回答问题，但忘记名字了，可以直接叫张三同学后面一位来回答
* 通过va一族来实现 <stdarg.h>
    * 变量：va_list类型，获取a往后的参数列表
>​			va_list arg;

  * 
    * 函数
* 
     * 
          * va_start：定位到变参列表第一参数的位置

>​				va_star(arg, n);  // n为变参列表的前一个变量
* 
  * 
    * va_arg：返回下一个类型匹配的表达式
>​				va_arg(arg, int);
* 
  * 
    * va_end：结束对可变函数实参的遍历，清除va_list空间
>​				va_end(arg);
* **代码**

*  <img src="https://i.loli.net/2020/11/16/Le6KwXD7qYNBd3a.png" alt="图片" style="zoom:80%;" />

* 细节见注释，包含头文件、va_arg类型匹配、最多遍历次数、va_end清空、int最小值
* 当不确定某方法所在的头文件时，使用gcc编译可能会提示需要包含的头文件

*  <img src="https://i.loli.net/2020/11/16/s6ZPm2bQu9Sfhxy.png" alt="图片" style="zoom: 50%;" />

​	不过在本机的Xshell上就算使用-Wall也没有显示，老师使用的是Mac

# 随堂练习

* <img src="https://i.loli.net/2020/11/16/drFGLz7t5eEohYx.png" alt="图片" style="zoom:50%;" />
* **代码**

*  <img src="https://i.loli.net/2020/11/16/cunTHDAwmxLFz5j.png" alt="图片" style="zoom:80%;" />

​				没有考虑负数输入，但不是重点~

# 代码演示

## 简版printf函数的实现

1. 实现打印字符的功能（从0到1的一步）
    1. putchar('x')函数：向标准输出输出一个字符'x'
2. 输出字符串功能："hello world"，并有返回成功打印字符数的功能

* <img src="https://i.loli.net/2020/11/16/lYmf4ACihZUagK3.png" alt="图片" style="zoom:80%;" />
* 系统会自动给字符串末尾加**'\0'**，其对应十进制值为0；eg. 'a'→97
* const修饰符是为了让传入的**字符串字面量**不被修改，也不能被修改
    * 在C中不会警告，但在C++中会报警告，如下：

    * <img src="https://i.loli.net/2020/11/16/L9oQlPAKTpDNjbn.png" alt="图片" style="zoom:80%;" />
      
    * 另：char *const frm
        * 不能修改frm指针（如char *p; frm = p;），但是可以修改该指针指向的内容
        * 参考[const char * 、char const *、 char *const三者的区别](https://blog.csdn.net/SilentOB/article/details/76994618)-CSDN，前两者相同
* 字符指针取字符串第i位的值可以用两种方式
    * frm[i]
    * *(frm + i)
3. 让%d控制字符生效，输出整数
    1. 向屏幕输出'%'

* <img src="https://i.loli.net/2020/11/16/ChxOHobWrjXtqzR.png" alt="图片" style="zoom:80%;" />

    * 使用switch结构
    * break前面不能是逗号，需要是分号

  2. 向屏幕输出正整数123

* <img src="https://i.loli.net/2020/11/16/nPG8ak69YBsEXTH.png" alt="图片" style="zoom: 67%;" />
  
  * 通过两个while循环，将数字翻转再翻转输出
    * 不翻转的话，只能从低位开始遍历输出
  * 数字+字符'0'（即48），可以将数字转为字符输出
  * ❌无法输出整数0
  3. 向屏幕输出正整数0
  
* <img src="https://i.loli.net/2020/11/16/eNfsqSaWV152HPn.png" alt="图片" style="zoom:80%;" />

* do...while与while的区别
* ❌对于正整数1000，输出为1

  4. 向屏幕输出正整数1000

* <img src="https://i.loli.net/2020/11/16/7V9SChJGKqocPp5.png" alt="图片" style="zoom:80%;" />
  
* 记录数字位数，考虑0值，换成do...while方式
* 第二次翻转可以使用do...while(--digit)
    * 但为了避免digit为0时陷入死循环，使用while(digit--)先判断digit位数更保险
    * 虽然digit为0几乎不可能出现
* ❌错误样例
  
* <img src="https://i.loli.net/2020/11/16/ylMj7kSoLbeP1Of.png" alt="图片" style="zoom:80%;" />

* digit--时，输出数字1000时，会多一位
    * --digit则会陷入死循环，无限循环输出0
        * 因为输入为0时，记录的数字位数为0（见下），--digit就变成了-1
* 第一个while循环计算0的位数时，为0，有误！

* ❌对于负整数，输出有误！
  
  5. 向屏幕输出负整数-123

* <img src="https://i.loli.net/2020/11/16/4DB1z6pvaRe5cWG.png" alt="图片" style="zoom:80%;" />

* 只需加个负数判断
* ❌对于INT32_MAX、INT32_MIN，输出有误！

  6. 向屏幕输出INT32_MAX(2^31 - 1)、INT32_MIN(-2^31)

* <img src="https://i.loli.net/2020/11/16/8UnWAOowcXLZFtD.png" alt="图片" style="zoom:80%;" />

* INT32_MAX翻转后，int类型表示不下→切块翻转
    * 5位5位切成两部分→两部分分别翻转→两部分分别翻转输出
    * 24174 / 83647      →47142 / 74638   →24174 83647
    * **代码**
            
        * <img src="https://i.loli.net/2020/11/16/s4hxpwjFivyTaNM.png" alt="图片" style="zoom:80%;" />
        * rev_num传temp1地址才能使其值能被修改
        * output_num所做的返回位数操作，我认为可以直接通过digit1、2得到
        * 记得修正高5位、低5位的实际位数
        * rev_num和output_num函数如下：
          * <img src="https://i.loli.net/2020/11/16/fueZTx29zskOR7j.png" alt="图片" style="zoom:80%;" />
            * output_num里PUTC并不生效，考虑编译时其顺序在PUTC定义前
        * ❌对于INT32_MIN，输出还有误！
    
* 此时INT32_MIN输出还是不对，因为
  
* <img src="https://i.loli.net/2020/11/16/56jYUslPg7I4mEi.png" alt="图片" style="zoom:80%;" />
  
* INT32_MIN取负数还是它本身，正数可表示不下了
    * 负数绝对值比正数大一
    * INT32_MIN：1000...000
        * →⭐**求相反数**：取反+1→0111...111 + 1
        * →1000...000（本身）
    
     * **代码**

        * <img src="https://i.loli.net/2020/11/16/dg82JVpNuD1klcC.png" alt="图片" style="zoom:80%;" />

        * 使用无符号整型**uint32_t**来存储即可！

4. 让%s控制字符生效，输出字符串

* 很简单，加入一个case即可
    * <img src="https://i.loli.net/2020/11/16/3FuybAkoQNm7Zrl.png" alt="图片" style="zoom:80%;" />
    * 注意字面量要用const char *

5. 检验返回值功能

*  <img src="https://i.loli.net/2020/11/16/C1FZpecRQs8JMPt.png" alt="图片" style="zoom:80%;" />

*  <img src="https://i.loli.net/2020/11/16/vrMjaIJBLTuozq6.png" alt="图片" style="zoom:80%;" />

# 附加知识点

* K&R风格的函数定义

  * <img src="https://i.loli.net/2020/11/16/s9DeIKVOoQXYMHU.png" alt="图片" style="zoom: 50%;" />

  * 对参数列表里参数的声明放在了后面
  * 上古级别的写法，了解即可，不推荐使用
* 使用%g控制字符可以以更友好的方式输出数字的有效位

*  <img src="https://i.loli.net/2020/11/16/s5AE1XFV8oZkm7P.png" alt="图片" style="zoom:80%;" />

* 数组：展开的函数；函数：压缩的数组
* [int和long的区别](https://www.jianshu.com/p/c8e35e118d73)-简书
    * 不同位数系统下大小不同
    * long -> 32位：4字节；64位：8字节
    * 但int都是4字节，long long都是8字节
* 算法是什么？聪明人的做事方法
* ⭐while(~scanf("%d\n", &a))
    * while(scanf("%d\n", &a) != EOF) 可以用while(~scanf("%d\n", &a))代替
    * ~是位取反，EOF即-1，按位取反即为0
* undef宏定义时只需加宏名，不需要加参数，如：
>  #define PUTC(a) putchar(a)
>  #undef PUTC
* ⭐**二进制求相反数**：取反+1
    * 无论正负，是互逆的，两次该操作则回到本身
    * 与-1再取反一样
# 思考点

* 💡va_arg对于类型不匹配时是怎么处理的？

  * <img src="https://i.loli.net/2020/11/16/GldqWCwOgRhpKvt.png" alt="图片" style="zoom:80%;" />
  * <img src="https://i.loli.net/2020/11/16/HZ3pLYmJGn4O1va.png" alt="图片" style="zoom:80%;" />

  * 对于输出有两个问题：
    * 第一行，第一个int值获取不到时，输出的是0.000000，第二次却是nan？
    * 第三行，输入的全是int类型，但却获取到了第二个数字：3.000000，实验证明，这个数字和第二行的3.000000有关，是因为va_list没有清理干净吗？
  * ❓对于什么类型就会往后走多少长度的地址去获取值
      * 如double，就是往后获取8个字节对应的变量
  * 💡但对于覆盖还是未清理的问题，不知道无法解释~
  * 如果能打印va_list的地址就更好了~
# Tips

* 刷刷OJ
* 参考工具书第7章

---


