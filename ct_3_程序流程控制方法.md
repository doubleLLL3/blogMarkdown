---
title: '3 程序流程控制方法'
date: 2020-10-17 18:10:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## if语句、switch语句

* （C语言关系运算符）
    * 0 → false；1 → true
    * false 👉 0、NULL(null)、'\0'
    * <= → =<
    * ! 取非 （关系运算符）
        * 要注意区分：位取反运算符 ~ （位运算符）
        * ⭐!! 非非操作：逻辑上的归一化
            * 将所有真值统一到1，假值还是0
* 顺序结构
    * 略
* 分支结构
### IF语句

*  <img src="https://i.loli.net/2020/11/16/E3VPF2KyuB1XZU9.png" alt="图片" style="zoom: 50%;" />

* (表达式)
    * **任何表达式都有逻辑返回值**
    * 对于赋值表达式 a = 123;
        * 返回值为a变量的值123
* {代码段;}
    * 了解什么是一条语句？
        * 单语句：“;”结尾的语句
            * int a = 1, b = 234;  // 是一条语句，“,”前后分为为一个表达式
        * 空语句：只有一个“;”，没有作用
        * 复合语句
            * 用花括号
            * **即代码段👆**
* if else语句
    * 可以用问号表达式（三目运算符）来代替
        * ...? ...: ...;
### SWITCH语句

*  <img src="https://i.loli.net/2020/11/16/lr4Wdi1YQLVJutF.png" alt="图片" style="zoom: 50%;" />

* 变量a要有一个**明确的唯一的整型值**作为映射！
    * 字母也有这样的映射：ASCII码
* 进入case后，依次执行后续代码
    * 直到遇到**break、continue或结构末尾**~
    * 而不需要再判断其后的case是否满足
* default分支
    * 当case**都不满足**时，会进入，相当于if else语句的else
## CPU的分支预测

从力扣题-**回文整数**引出

*  <img src="https://i.loli.net/2020/11/16/Pq3WLClvRAoancO.png" alt="图片" style="zoom: 50%;" />

船长超高时间效率代码如下：

*  <img src="https://i.loli.net/2020/11/16/6YQbOeJdA9CwmPK.png" alt="图片" style="zoom: 50%;" />

*  红框处实现的就是x<0
   *  用来筛选小于0的整数，负数肯定不是回文整数

*  这段语句在操作系统内核里出现：

*  <img src="https://i.loli.net/2020/11/16/blxuFpUV6qitKZM.png" alt="图片" style="zoom: 50%;" />

* !!(x) 逻辑归一化，结果只会是1或0
    * 经常成立与否的情况会告诉正在处理程序的CPU

⭐CPU执行程序过程解析

* C语言程序执行过程：编译后生成可执行文件→加载到内存里→CPU执行
* cash 缓存
    * 一级缓存：速度最快，而容量最小
* CPU指令执行方式对比
    * 早期串行（取址、指令预解析、写数据、执行、写回内存）
    * 当今并行
    * 示意图如下左右：
      * <img src="https://i.loli.net/2020/11/16/Ae2yEuMHxXrWDU7.png" alt="图片" style="zoom: 50%;" />
    
      * 对于5条指令
        * 串行方式需要5 * 5 = 25个时钟周期
        * 并行方式只需要5 + 4 = 9个时钟周期
      * 当指令非常多时，提高效率将近5倍
* <u>**所以**</u>！CPU**喜欢顺序结构**
    * 讨厌分支结构（要少用if else语句）
    * 分支预测问题
    * 对于并行的方式，CPU不知道分支结构下一步是谁，该提前加载哪一步？
        * CPU实际是随机加载下一步
            * 所以，如果预测错了，后面就白做了，此时又要返回重新处理
* 而__builtin_expect()做的就是**告诉**CPU哪种分支情况更易发生！！！
## 循环结构

* WHILE语句
    * while () {代码块;}
        * 先判断，再执行
    * do {代码块;} while (表达式);   ←分号要注意！
        * 至少执行一次
* FOR语句
    * for(初始化; 循环条件; 执行后操作) {代码块;}
    * 让循环变得很秀，由三部分组成
    * 这三部分可以都不写
        * 例如：for(; ; );，那就相当于while (1);
        * 上面是一个死循环，啥都没有
# 随堂练习

* <img src="https://i.loli.net/2020/11/16/PoRm8Au9lVJkGxN.png" alt="图片" style="zoom: 50%;" />
  
* 记得条件判断冗余检查
* 输入非法时（如"q"）陷入死循环问题
      
    * 解决方法及思考过程详见下文：思考点1
* **代码**
    * 冗余版
      
    * <img src="https://i.loli.net/2020/11/16/qVUKd9tw3lBFzLa.png" alt="图片" style="zoom: 80%;" />
      
    * ❗这里scanf的\n是隐患！要去掉\n，否则需要多读一个数
        * 因为在输入下个数时才读到\n
        * ⭐参考博客：[用 "%d\n" 要多读一个数？ —— scanf() 函数的那些坑](http://www.shaoguoji.cn/2017/05/23/weird-scanf/)
                    
            * <img src="https://i.loli.net/2020/11/16/5v6gUmWrtR9neQz.png" alt="图片" style="zoom:67%;" />
        
    * 精简版

*  <img src="https://i.loli.net/2020/11/16/QmkTcxDyI1eh3wr.png" alt="图片" style="zoom: 80%;" />

* ①<img src="https://i.loli.net/2020/11/16/FfVExLjrdUaKuvs.png" alt="图片" style="zoom: 50%;" />
* ②<img src="https://i.loli.net/2020/11/16/7un3Hh5ZkFcPsIv.png" alt="图片" style="zoom:50%;" />
    * 熟悉switch语句结束的特性
    * **代码**

*  <img src="https://i.loli.net/2020/11/16/fLopnCaXK3AJFsU.png" alt="图片" style="zoom: 67%;" />

* <img src="https://i.loli.net/2020/11/16/NWnoP83eSqKsX6r.png" alt="图片" style="zoom: 50%;" />
* **代码**

*  <img src="https://i.loli.net/2020/11/16/PLNASoUsfzuFZQw.png" alt="图片" style="zoom: 80%;" />

​			对于while方式，如果**首次判断成立**，则与do...while方式没有区别

* <img src="https://i.loli.net/2020/11/16/zs7Z4McuEr5J1YD.png" alt="图片" style="zoom: 50%;" />
* **代码**

*  ![图片](https://i.loli.net/2020/11/16/lg1nwWE436jJvKx.png)


## 亮点笔记

* ⭐对条件判断语句进行冗余检查（剪裁），通常可以有更精简的代码
    * 所有判断表达式是否为0的情况，可以直接使用 if (!表达式)
        * 对于if (表达式) 单语句，可以利用逻辑与
            * (表达式) && 单语句
            * 比如 i && printf(" "); ，用来在除第一次循环中输出空格
            * 单语句不能是{代码块;}
    * if...else用三目运算符代替
* 一般将循环变量i定义在初始化部分：int i = 0
    * 不需要提前放在前面
        * 更规范、简明：用变量之前再定义，不要离的太远
    * 作用域问题
        * 只在循环里用到该变量
* ++i 比 i++ 快？
    * 能用 ++i 就用
        * 从函数栈的角度：👇
        * ++i 直接入栈 i+1 的值
        * 而 i++ 的话，先入栈 i 再入栈 i+1

# 代码演示

## 分支结构（6.struct_program.cpp）

* <img src="https://i.loli.net/2020/11/16/HLe6A4pgkxCKUJF.png" alt="图片" style="zoom:80%;" />
  
* a - b可以代替a == b判等
* if else可以用三目运算符代替
* <img src="https://i.loli.net/2020/11/16/ckP8NlW6wvzg9GE.png" alt="图片" style="zoom:80%;" />
  
* **输出1：false**
    * a++  和 && 的运算优先级？
        * ++优先级大于&&
        * a++外面的()为什么没有把++的运算优先级提高到比&&先加呢？
            * 是优先的
            * 这里输出false与此无关~
                * 应该**对于if判断**来说，先判断a，再去++
                * a++表达式的值是没有++的a
* **输出2：a = 1， b = 0**
    * ⭐逻辑与--短路规则
        * 只要前面有假值，就不会往后走了【聪明人的做法】
* <img src="https://i.loli.net/2020/11/16/mjKH45IbMVkWN9z.png" alt="图片" style="zoom:80%;" />
* **（接上a = 1, b = 0）**
* **输出1：true**
* **输出2：a = 2， b = 0**
    * ⭐逻辑或--长路规则
        * 只要前面有一个真值，就不用了往后走了
        * 要想执行后面的表达式，必须前面的表达式均为假值
* <img src="https://i.loli.net/2020/11/16/RIfth8KzNTkgaDv.png" alt="图片" style="zoom:80%;" />
  
* 以空格为间隔（末尾无空格）的输出方式参考
    * 方式一：如果不是第一次循环，输出前空格
        * 首次更好得到：i == 0
        * **可优化第43行**：i && printf(" ");
    * 方式二：如果不是最后一次循环，输出后空格
* rand()
    * 需引入<stdlib.h>
    * 输出的是随机的无符号数
        * rand() % 100可以随机输出0-99的数
    * ⭐其实是固定的随机
      
        * <img src="https://i.loli.net/2020/11/16/oYWifhFTJVSX1gD.png" alt="图片" style="zoom:80%;" />
        
        * 通过 **srand()** 设定随机种子
            * srand(time(0))
                * time(0)
                    * 获取当前的时间，需引入<time.h>
                    * 精确到s，1970.1.1至今的秒数
            * 可以看出值是变化的了，但计算机中没有真正意义上的随机
                    
                * <img src="https://i.loli.net/2020/11/16/bAeSVG1r5RXChmO.png" alt="图片" style="zoom:80%;" />
    
* **优化版**
       
    * 上述代码有三处可以简化！两个if都可以去掉
      
    * <img src="https://i.loli.net/2020/11/16/STUjocDaFEh7QAK.png" alt="图片" style="zoom:80%;" />
      
    * ⭐~
        1. 位运算与取余的转换：% 2 等价于 & 1
            * % (2 ^ n) 等价于 & (2 ^ n - 1)
        2. +1与真值1的等价
        3. 逻辑与的短路规则
## 循环结构（7.cpp）

**判断回文整数**（十进制）

*  <img src="https://i.loli.net/2020/11/16/xVAUFIyatOKlL2o.png" alt="图片" style="zoom:80%;" />

* 记得对负数做特判！
* 如果想判断二进制下的回文整数呢？
    * 将圆圈处都改成2即可
        * 计算机底层其实都是以二进制存储数据的
        * 不管什么进制下，都可以判断回文
    * base进制下回文整数判断
    
        * <img src="https://i.loli.net/2020/11/16/iCQ2woy9aVBnDp3.png" alt="图片" style="zoom:80%;" />

**计算整数位数**（十进制）

* while循环和do...while循环的细微区别比较
    * 关键在于第一次循环的条件是否成立~
        * <img src="https://i.loli.net/2020/11/16/hSO7N1q6MUY9C2I.png" alt="图片" style="zoom:80%;" />
        * 当输入非0数字时，digit和digit2没有区别
    * **当输入数字0时，有区别**
      * <img src="https://i.loli.net/2020/11/16/Cnhc1b6WjJyxUEt.png" alt="图片" style="zoom:80%;" />
    
      * 所以有0的情况判断位数
                     * 用do...while
                  * 或者特判
# 附加知识点

* switch语句
    * case后面不允许声明变量
    * default的末尾也要加break吗
        * 可有可无，加break是为了统一风格
    * Python不支持switch语句
* C语言中，如果main函数的末尾没有return语句将会有什么影响?
    * C99开始，基本没影响，标准要求等价于**自动补上**return 0;
    * 而C99之前，如果控制抵达main函数结尾而没有return，则为**行为未定义**。
* 浮点数判等
    * 不直接用==，会不准确，利用差值是否小于某个极小值来判等
* __builtin_expect()还有6+个兄弟

*  <img src="https://i.loli.net/2020/11/16/rZ9mWqu1IYb8ywz.png" alt="图片" style="zoom: 50%;" />

* 位权决定左移右移的倍数
    * 十进制，左移一位，*10
    * 二进制，左移一位，*2
# 思考点

* 💡（解决）对于5.if.cpp，即随堂练习1。输入非法值，如字母‘q’，会陷入无限循环
    * 与‘q’的ASCII码有关吗？
        * 无关~
        * 以%d形式输出‘q’，看起来是一个地址，单次运行是固定的，应该是初始化n时随机分配的一个值
            * 如果输入正常值再输入错误值，此时该值就是之前的n值
            * 因为其实没有读入值来改变n
    * 读不进‘q’值
        * scanf的返回值为0
        * 但是也不会移向下一个输入，类似第一讲里的%[^\n]遇到\n无限循环事件
    * **⭐当然！** 同样可以使用getchar()吞掉这个非法值
        * 输入qw属于非法吗
            * 是的，需要getchar()吞两次
    * **改后代码**
    
        * <img src="https://i.loli.net/2020/11/16/qGIxEy9dpkn3uJh.png" alt="图片" style="zoom:80%;" />
    
        * getchar()放在if(!ret)里更好，只有在读入非法字符时才吞字符
            * 上图的代码：getchar**会吞空白符和非法字符**
            * 下图的代码：**只会吞非法字符**
              
                * <img src="https://i.loli.net/2020/11/16/jNOA3VU6QbFCMtn.png" alt="图片" style="zoom:80%;" />
              * 为了演示吞掉的字符没有空白符只有非法字符，这里加了printf函数

*  <img src="https://i.loli.net/2020/11/16/Nwr2uETJfcjXMmC.png" alt="图片" style="zoom: 67%;" />

* 💡同样，对于5.if.cpp（有循环，需手动停止），用>output将标准输出重定向时，会遇到输出不写入重定向的文件的问题
    * 具体情况已在海贼QA提问，期待探讨：[输出重定向在什么条件下才将输出写入文件呢?（stdout、./a.out > output、Ctrl+C/Ctrl+D）](https://xue.kaikeba.com/qa/?qa=840/stdout-a-out-output-ctrl-c-ctrl-d)
    * Ctrl+C不写入，**Ctrl+D写入**
>Ctrl+C：Linux下默认的**中断键**，当键入此键时，系统会发送一个中断信号给正在运行的程序和shell。
>Ctrl+D：Linux下标准输入输出的**EOF**。在使用标准输入输出的设备中，遇到该符号，程序会认为读到了文件的末尾，因此结束输入或输出。

* 如果有非法输入导致有大量输出时，Ctrl+C也可以写入
    * **疑问**：这是因为缓存不够，提前强制输出到output文件了吗？
        * 是这样理解
    * **疑问**：那么这部分写入output文件的输出仅仅是缓存溢出的输出，还是Ctrl+C前的所有输出呢？
         * 溢出的
* 代码5.switch.cpp：为啥输入字母之类的会无限循环?
    * 同5.if.cpp，见思考点1
# Tips

* OJ做题方式
    * 在vim里编写代码
    * cat显示代码复制出来
    * 提交
* 经典题：已知年月日判断是否合理
    * 有没有必要用那么多的if else？
    * 可以用空间换时间：把每月的天数用数组存起来
* 参考工具书第6章 6.4节

---


