---
title: '0 从C到C++'
date: 2020-12-11 18:40:00
tags: [计算机,C/C++,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
>C++比C难学，究竟难在哪里？
# 课程内容

## C++的语言特性

* 【头文件】是C语言的3倍
* 除了面向过程编程，还额外添加了3种【编程范式】
    * 面向对象编程：类和对象
    * 泛型编程：模板，开发一个函数，相当于开发一百个函数
    * 函数式编程：Lamda
    * [PS]
        * 不同的编程范式对应不同的开发效率
            * 开发效率 -> 写代码+测试+维护
            * 开发效率：面向对象 > 面向过程 [后期维护成本低]
        * C语言也可以实现，但是很麻烦
        * 越往下越难，工作中主要同时使用前两种 [面向过程、面向对象]
* 设计哲学：程序的逻辑错误优先表现为编译错误，再表现为运行时错误 ->【异常处理】
* 【STL】惠普实验室开发
## 输入输出的程序对比 [C、C++]

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113735.png" alt="图片" style="zoom:67%;" />
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113742.png" alt="图片" style="zoom:67%;" />
* cin、cout
    * 不需要格式占位符
    * 左移、右移运算符 [<<、>>]：涉及运算符重载
* printf
    * "%lf"：默认保留6位小数
    * "%g"：输出全部有效数字位数，cout使用的规则
* scanf、printf是函数；而cin、cout不是函数，是对象
* [PS] endl：换行 + 清空缓冲区
## 内置数据结构

queue、stack、string、unordered_map、map...

* 相比自己用C语言实现，这里很方便地支持[任意类型]的元素
    * 详见[《面试笔试算法上》——5 STL“容器”的使用与练习](https://doublelll3.ml/mbss_5_STL%E2%80%9C%E5%AE%B9%E5%99%A8%E2%80%9D%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E7%BB%83%E4%B9%A0/)
* string
    * C的strcmp与C++的==
    * C的strcat与C++的+=
    * C的strlen与C++的length()
        * 实现原理不太一样
        * strlen：每次都需从头到尾扫一遍字符数组，直到遇到'\0' --> O(N)
        * .length()：成员属性 --> O(1)
* map
    * 基于哈希表 [无序]
        * 存储、查找的均摊时间复杂度：O(1)
        * hash_map [非标准，C++11之前]
        * unordered_map [C++11标准]
    * 基于红黑树 [有序]
        * map
    * 外在表现类似数组，但功能更强大
    * find(key)：如果找不到，返回end() [哈希表的结束位置]
# 代码演示

## strlen与length()的比较

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113750.png)
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113800.png" alt="图片" style="zoom:67%;" />
* 耗时有略微差异，但差异不大，原来环境可能对strlen有优化
    * 参考[for(i=0; i<strlen(s); i++)会每次都执行一遍strlen吗？](https://www.zhihu.com/question/343176606)——知乎
* c_str()—[cppplus](http://www.cplusplus.com/reference/string/string/c_str/)
## map和unordered_map的比较

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113806.png)
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113813.png" alt="图片" style="zoom:67%;" />
* 迭代器的使用先感受下，类型定义明确标明，后面可用auto关键字代替❗
* 有序性体现在遍历元素时
    * unordered_map对于key和value都无序，而且还会打乱原始顺序
    * map可当成排序工具
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113820.png" alt="图片" style="zoom:67%;" />
        * auto关键字的使用
            * 对比前面完整的迭代器类型定义，先感受下
            * 很强大，有点像cin和scanf的区别，可以自动判断iter的类型
        * 排序结果
            * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113827.png" alt="图片" style="zoom:67%;" />
            * 虽然看着有点像计数排序，但不是
            * 时间复杂度涉及底层红黑树：O(NlogN)
## sort

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113834.png" alt="图片" style="zoom:67%;" />
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113841.png" alt="图片" style="zoom:67%;" />
* 展示了C++的4种编程范式
* CMP()为可调用对象
* lamda表达式为C++11新推出的
* 想到这里struct和class有什么区别，参考[深度理解：struct和class的区别](https://www.topomel.com/archives/1297.html)——博客
# 随堂练习

## [HZOJ-245](http://oj.haizeix.com/problem/245)：货仓选址

![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113848.png)

**样例输入**

```c++
5
1 3 5 6 10
```
**样例输出**
```c++
12
```
* 思路
    * 输入可能是乱序
    * 问题转换
        * 随机选择一个点p，当前距离总和为s1
        * 当点p移动很小的距离Δ时，此时距离总和s2 = s1 + (n1 - n2) * Δ
            * n1：点p左边的商店数；n2：点p右边的商店数
        * 【目标】s2 < s1
            * 当n1 - n2 < 0时，Δ > 0 --> s2 < s1：即右边商店多时，往右移动点p，可以有更优解
            * 当n1 - n2 > 0时，Δ < 0 --> s2 < s1：即左边商店多时，往左移动点p，可以有更优解
        * 【最终目标】n1 = n2 即可，所以找第 n / 2 位元素坐标 [n为商店总个数，位数从0开始]
* 代码
    * 解法1：sort方法
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113856.png" alt="图片" style="zoom:67%;" />
        * 主要耗时在输入和求和
        * sort使用详见[《面试笔试算法上》——2 二分专题——附加知识点](https://doublelll3.ml/mbss_2_%E4%BA%8C%E5%88%86%E4%B8%93%E9%A2%98/#%E9%99%84%E5%8A%A0%E7%9F%A5%E8%AF%86%E7%82%B9)，终止位置为最后一个元素的下一位
    * 解法2：nth_element方法
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113903.png" alt="图片" style="zoom: 67%;" />
        * nth_element
            * 基于[快速选择算法](https://zhuanlan.zhihu.com/p/64627590)——知乎：借鉴快速排序的Partition过程，但不会对整个序列排序
            * 执行完后，【第k位放置着从小到大排名第k位的元素】❗
    * 比较
        * 解法1：完全排序
        * 解法2：不完全排序，直接得到第k位
## [HZOJ-166](http://oj.haizeix.com/problem/166)：字符串操作1

![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113910.png)

**样例输入**

```c++
AAAAAA
2
xxx
```
**样例输出**
```c++
6
AxxxAAAAA
6
```
* 思路
    * 考察字符串操作和常用方法
    * 将字符串B插入到字符串A里面
* 代码
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210125113925.png" alt="图片" style="zoom:67%;" />
    * string的[size()、length()](https://en.cppreference.com/w/cpp/string/basic_string/size)是一样的
    * string的[insert](https://en.cppreference.com/w/cpp/string/basic_string/insert)、[rfind](https://en.cppreference.com/w/cpp/string/basic_string/rfind)[返回的时正向的偏移量]，学会查看[cppreference](https://en.cppreference.com/w/)
    * 在这个场景里，[find_last_of](https://en.cppreference.com/w/cpp/string/basic_string/find_last_of)方法也可以，它关注字符串中任意一个字符的匹配
        * 参考[string的find和find_first_of的区别](https://www.geek-share.com/detail/2712377515.html)——极客分享
# Tips

* 不仅要知其然，还要知其所以然，更要知其四五六个所以然
