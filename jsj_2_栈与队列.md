---
title: '2 栈与队列'
date: 2020-11-12 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 队列

* FIFO（First In First Out）、LILO。类似排队，区分先来后到
* 也是顺序结构，需要连续存储空间（不过也可以用链表实现）
### 结构定义

* 需要连续的存储空间
* 容量
* 队首、队尾
* +元素个数：用来判断队列是否已满，方便**循环队列**解决假溢出问题
* 数据类型
### 结构操作

* 出队
    * head - 1
    * count - 1
* 入队
    * tail + 1
    * count + 1
* ❗假溢出
    * 不能存元素了
    * 实际上可能还没有满：出队操作使队列头部有空位
    * 解决方案：**循环队列**
        * tail走到头了再回到队列前面
        * 使用取余运算确定位置：(tail + 1) % length
* 扩容：遇到真溢出时，详见代码演示
## 栈

* FILO（First In Last Out）、LIFO。类比装书的箱子，一头是堵死的
* 也是顺序结构
### 结构定义

* 需要连续的存储空间（有大小限制）
* 容量
* 栈顶指针【top】：对于空栈，top = -1，为0可不合适
* 数据类型
### 结构操作

* 出栈：top - 1             // 需要判空
* 入栈：top + 1，存值 // 需要判满
## 应用

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/7ZeM7Gt.png" alt="图片" style="zoom: 50%;" />

* DFS和BFS详见《面试笔试算法上》 - 6 搜索专题
* 单调栈、单调队列自行学习
# 随堂练习

## [LC-20](https://leetcode-cn.com/problems/valid-parentheses/)： 有效的括号

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/hiUrFW5.png)

* 关键在于思维：大问题转小问题
    * 问题简化成只有一种括号，怎么做？可以不使用栈完成吗？
    * 思路：只需要记录左括号数量和右括号数量
        * 在任意位置，左数 >= 右数
        * 在最后位置，左数 == 右数
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/MvBFcyV.png" alt="图片" style="zoom:50%;" />
        * 其实可以只用一个数top记录，遇到左括号+1，遇到右括号-1
            * 在任意位置，top >= 0
            * 在最后位置，top == 0
    * 这个+1、-1操作，可以联想到栈的入栈、出栈吗？
    * 一次括号匹配相当于入栈出栈一次
    * 括号里的括号相当于完全包含关系
    * 也有分治思想
* **栈**：可以处理具有完全包含关系的问题
* 代码
    * 可参考《面试笔试算法上》- 5 STL“容器”的使用与练习 - HZOJ-378讲解
    * C语言中，则先实现数据结构-栈，再用栈解决问题
# 代码演示

## 队列

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/HxXwsau.png" alt="图片" style="zoom:67%;" />

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/XMnTscH.png" alt="图片" style="zoom:67%;" />

部分结果

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/0TNyAaM.png" alt="图片" style="zoom:67%;" />

* 定义tail有两种方式
    * ①指向最后一个元素的地址
    * **②**指向最后一个元素的下一个地址（本代码的选择）
* 使用循环队列，已经不存在假溢出的现象
* 但对于真溢出怎么办呢？👇
### +扩容

针对循环队列的扩容

* 3种动态开辟内存的方法【malloc、calloc、realloc】，使用哪一种？
* 🆒malloc、calloc都可以，但是
* ❗ 之前使用的realloc不好使了
    * 循环队列的尾可能不在头的后面，而是尾和头重合了
    * 举例：当队列已满时，head、tail（这里tail定义为尾部元素的后一位）在如下位置，此时扩容一倍，扩容后队列的push和pop会怎么样呢？
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/znSbZ29kmxo8jRO.png" alt="image-20201202094139099" style="zoom:60%;" />
    * 此时从head→tail之间插入了很多空值，因为realloc是把值按原顺序搬过去，而扩容部分还没有值
    * 那么，不管是从head到tail出队[中间夹杂了很多空值]，还是从tail入队[tail位置存在未出队的元素]，都存在问题！

*    而用malloc开辟空间后，把原队列从head到tail按顺序迁移，即手动将head调整为索引0，如下，关键在于**头尾指针的调整**！
     * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/DhH3TjPxViYMCQL.png" alt="image-20201202094256207" style="zoom:60%;" />
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/wUHz7at.png" alt="图片" style="zoom:67%;" />
    * 关键在于调整指针为正常顺序
## 栈

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/pknuxKj.png" alt="图片" style="zoom:67%;" />

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/49VXiRB.png" alt="图片" style="zoom: 67%;" />

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/smJHAhA.png" alt="图片" style="zoom:67%;" />

* 第72行，pop操作只需要top - 1即可
* 第109行，printf里若有待执行命令而不是简单的取值时，需注意顺序
    * 其调用顺序，与系统栈的设计有关
    * 参考[printf函数参数从右至左压栈](https://blog.csdn.net/buaa_shang/article/details/23162679)-CSDN；[printf压栈出栈](http://printf压栈出栈)-CSDN
        * 这种**从右至左**的方式**不会**随着编译器、机器的不同而不同
        * 涉及底层，先不深究
    * 【结论】 printf的参数有待执行的命令时，尽量分开写
* 第106行，在出栈前需先判空，否则top = -1会带来问题
    * 首次出栈结果，已安全
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/XeGvxxp.png" alt="图片" style="zoom:67%;" />
* ❓应该在调用empty和top前，确定栈是否存在，而不在函数里写判NULL操作
    * 而其他操作基本都有判NULL操作，主要和方便程序拆分有关，不用细究
    * 都写判NULL操作也可
# 附加知识点

* 系统栈
    * 也是栈，大小：8MB
    * 递归时，使用的就是系统栈
# Tips

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/G6xCZzy.png" alt="图片" style="zoom: 33%;" />

---


# 课程速记

