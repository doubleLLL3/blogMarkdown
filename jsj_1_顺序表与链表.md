---
title: '1 顺序表与链表'
date: 2020-11-10 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 引言

* 【基础数据结构】课程综述
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/VWYb1LV.png" alt="图片" style="zoom: 67%;" />
    * 重点掌握红色！
* 三个等式
    * 程序 = 算法 + 数据结构
    * 程序设计 = 算法 + 数据结构 + 编程范式
    * **数据结构 = 结构定义 + 结构操作**
* 通过算法与数据结构，使计算机合理利用资源，让计算资源更有价值
## 顺序表

【功能更高级的数组】

### **结构定义**

* 空间连续，可以存储任意类型的**相同**元素
    * 顺序表里也可以存顺序表
* size：顺序表的空间大小
* length：已存储的元素个数
* data_type：存储的元素类型
### **结构操作**

* 插入
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/dWtvTZh.png" alt="图片" style="zoom: 67%;" />
    * 👇👇👇
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/iYyFvkr.png" alt="图片" style="zoom:67%;" />
    * 不能空着插，必须连续
    * 插入位置后的所有元素向后移动一位
        * **从后开始**移动，每个元素向后移动一位
    * 必须要改变的属性：length + 1
        * size有可能需要改变，扩容请看下面
* 删除
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Mbe2n2c.png" alt="图片" style="zoom:67%;" />
    * 👇👇👇
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/PBMtVqA.png" alt="图片" style="zoom:67%;" />
    * 不是真的删除，而是告诉系统这个地址的存储区可以被占用、被覆盖
    * 将后面所有的元素整体向前移动一位
        * 相当于要删除的位置的值被覆写
        * 最后一个值怎么覆写？
            * 直接将length - 1
            * 其实都可以自定义设计
    * 必须要改变的属性：length - 1
* ⭐**添加扩容操作**
    * 动态内存分配函数
        * malloc：只是开辟空间，值不确定。举例：开房没有保洁阿姨
        * calloc：开辟空间，并且初始化。举例：开房后有保洁阿姨打扫清理
        * realloc：重新开辟空间。举例：升级房型
>​				void* realloc (void* ptr, size_t size);  // 返回新开辟空间的首地址

**3种情况【realloc】**

1. 先试探能不能在原地址后面直接加空间，空间首地址不变；如果不行，
2. 换个地址找找，申请空间，然后把原空间数据拷贝到新空间，并free掉原空间；如果不行，（自己可以设计减少扩容）
3. 就返回空地址，不清空当前地址
## 链表

### **结构定义**

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/RTiF5X5.png" alt="图片" style="zoom:67%;" />

* 程序内部 + 内存内部
    * 【程序内部】只暴露头指针
        * 头指针、小抓手、鸡妈妈：记录第一个结点的地址
        * 联想老鹰捉小鸡
    * 【内存内部】是连接的结点
        * 结点：数据域 + 指针域
        * 指针域
            * 最后一个结点的指针域为NULL
            * 单向链表的指针域又名“后继”；双向链表有“前驱”和“后继”
* 与顺序表都属于顺序存储结构
    * 链表思维逻辑上连续，但物理上不一定连续
### **结构操作**

* **插入**
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/96KhhMR.png" alt="图片" style="zoom:67%;" />
    * ①走到待插入位置的**前一个**位置的结点p
    * ②先将新的结点x的指针域指向待插入位置的结点p.next
    * ③将p的指针域指向新的结点x
    * 顺序不能变！否则可能引发内存泄漏（你想用已经用不了，但系统却以为你在用）
* 删除
    * 走到待删除结点位置的**前一个**位置
* 翻转
    * 方法一
        * 用一个新链表存，使用头插法
        * 不断在index = 0 的位置插入结点
        * 不足：浪费空间，麻烦
    * 方法二
        * 原地翻转，用两个变量倒，也是头插法
        * 前提：每次操作不要造成内存泄漏
        * NULL地址还是在最后面，不会翻转
* 代码演示见后
### 单向循环链表

* head永远记录的是**尾结点**的地址~
1. 对于需在index = 0插入结点的情况
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/x9C9IFV.png" alt="图片" style="zoom:67%;" />
    * 🆒当head记录的是尾结点时，**直接**在head指向的尾结点后插入即可
    * ❌如果head记录的还是首结点的地址，不好使，因为
        * 要找index = 0的结点的前一个结点，可能需要遍历完整个链表找到尾结点
        * 而不像单向链表那样，可以直接在head后、index = 0结点前插入
1. 对于在尾部插入结点的情况
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/pyOoZAj.png" alt="图片" style="zoom:67%;" />
    * 需要修改head指向新的尾结点【8】！
# 代码演示

## 顺序表

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/iCXZ9ho.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/4OlSv41.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/XqQ28Ef.png)

* 初始化、销毁
    * 使用malloc开辟空间初始化该结构
    * 从结构里到外free，再将指针置空
* 扩容操作
    * realloc返回地址先赋给中间变量，避免返回NULL的情况，导致原数据迷失
    * realloc成功后原地址空间自动被free
    * 可设计折半减小的扩容空间，直到确实一点空间都不剩返回失败标志0
    * 细节很多，详见注释
* 插入、删除
    * 注意不能操作的情况
    * 插入满员时，进行扩容操作
* 打印：人性化
* main函数测试：使用rand()、神奇的取模操作(得到数据、模拟各种样例、决定操作数)，记得clear空间
## 链表

![img](https://i.loli.net/2020/12/04/5Z8JytBu6KX2Ycw.png)              ![img](https://i.loli.net/2020/12/04/9XZyo4KhERbNTHF.png)              ![img](https://i.loli.net/2020/12/05/VdFSMtryBPgm4AN.png)        ![img](https://gitee.com/doubleL3/blog-imgs/raw/master/img/D2JC7XBg3qEjPk1.png)      ![img](https://i.loli.net/2020/12/04/3pG2oOz4kNh6ycD.png)      

部分结果

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/0MUbY8j.png" alt="图片" style="zoom: 67%;" />

* 虚拟头结点，方便插入和删除第一个结点
    * 使用普通变量定义head，❓考虑的是
        * 虚拟头结点比较固定，不需要随时free
        * 普通变量比指针变量更稳定，没必要malloc
# 思考点

* ⭐初始化数据结构[比如链表、链表结点]时，为什么要动态开辟内存[用malloc等]，而不是定义普通变量？
    * 首先，排除盲区：不是因为指针只能接受malloc返回的地址，指针也能指向普通变量
    * 关键：【malloc申请的内存在堆空间，普通变量定义在栈空间(在函数里面定义的)】
    * 栈空间：大小只有8MB；出了函数[作用域]变量就被自动释放了
    * 堆空间：可分配**大内存**；变量**生命周期长**，一般需手动释放
* ❓虚拟头结点定义为普通变量和指针变量的区别
    * 个人理解，用指针变量，是为了接收malloc返回的地址
    * 而虚拟头结点，在这只是一个指示作用，不需要大空间，所以不需要malloc，用普通变量即可
# Tips

* 学习编程不局限于语言
* 课后练习题

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/WE8asP6.png" alt="图片" style="zoom: 50%;" />



---


# 课程速记

