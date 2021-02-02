---
title: '4 线段树'
date: 2020-12-26 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
[学习数据结构主要关注解决的是什么问题]

# 课程内容

## 前置知识

* 解决的问题是什么？区间的修改与查询 [针对[积性函数](https://www.wikiwand.com/zh-cn/%E7%A9%8D%E6%80%A7%E5%87%BD%E6%95%B8)——Wiki]
* 问题背景
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744603501-oNJEaQmk0NcQBYnC.png" alt="图片" style="zoom:67%;" />
    * 单点修改、区间查询（基础版）
        * Modify(ind, val)：修改ind位置的值为val
        * Query(start, end)：查询start~end位置值的和
    * 区间修改、区间查询（进阶版）
    * 单点修改、单点查询（用不着线段树，数组即可）
    * 区间修改、单点查询（其实就是进阶版的特例）
## 基础版线段树

* 区间组成的树→区间的**和值**组成的树 [线段树]
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744932242-12cbmCOXTOF7SJEG.png" alt="图片" style="zoom:67%;" />
    * 👇
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744935674-YQhw3bQjX8ZqZuqv.png" alt="图片" style="zoom:67%;" />
    * 【构建过程】采用的分治的思想，将总区间分成左右两部分，一直进行下去，直到区间中只剩下一个结点为止
    * 【要维护的性质】结点统计的是一个区间的和值
        * 叶子结点代表了原序列中的单个位置的值
    * [PS] 回顾：树的结点代表集合，边代表关系
* **单点修改**
    * 递归，找到要修改的结点，修改，然后
    * 回溯，修改路径上每一个祖先结点的统计和值 [根结点到叶结点的路径]
* **区间查询**
    * 一个点可能代表一个区间的和值，查询更快
    * 如果不借助线段树，则需要一个一个查值
* ⭐线段树，其实是用来维护一维序列的高级数据结构
    * 时间复杂度 [与树高有关]
        * 单点修改：O(logN)
        * 区间查询：O(logN)
        * N代表一维序列的长度
* ❓ 问题思考
    * 若采用完全二叉树的存储方式，N个点的线段树最多需要多少个结点空间？【4N】
        * 先考虑用普通二叉树 [线段树一定是满二叉树] 存储，参考上图
        * 叶子结点数为N，则度为2的结点数为N - 1 [**二叉树**的性质：度为0的结点比度为2的结点多1个]，所以结点数为2N - 1
        * 而如果用完全二叉树的存储方式，则最多还要预留2倍叶子结点数的结点空间2N [**完全二叉树**的性质：两个子结点的索引与父结点i之间的关系是 2i 和 2i + 1 ]
        * 所以最多需要4N个结点空间
    * 如何做区间修改？ [大小为m的区间上每一个值都做修改]
        * 基于线段树的基本操作：O(m * logN)，相当于脱了裤子放屁
        * 在原有区间上修改即可：O(m)，比用线段树还好
        * 请看进阶版：O(logN)
* [PS] 只适用于单点修改、区间查询 【基础版】
    * 当面对区间修改的时候，基础版的线段树效率上还不如直接在一维序列上修改
## 进阶版线段树

### **区间修改 [⭐]**

* Modify操作
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744939616-A3tRUf0BKUhBjiYr.png" alt="图片" style="zoom:67%;" />
    * ⭐懒标记：不对其子结点立即发数值，碰到结点查询 [**修改操作**/查询操作遍历时] 时才下发
    * 类比[懒政]：皇帝 [根结点] 给百姓 [叶子结点] 发粮食，先发给上级 [子结点]，上级会先收着，等到皇帝亲自视察时才往下分发粮食
* Query操作
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744941416-RoKM4R0LwOHF2QjV.png" alt="图片" style="zoom:67%;" />
    * 触发懒标记下发数值
* 时间复杂度 [同区间查询操作]：O(logN)
### 关键词

* 完全二叉树 [存储结构]
* 区间 [每个结点的维护范围]
* 向上更新 [回溯过程]
* 下沉标记 [懒标记]
* ⭐口诀⭐ 下沉发生在**递归**之前，向上发生在**递归**之后
    * 标记下沉发生在递归之前，向上更新发生在具有修改操作的递归之后
# 随堂练习

## [HZOJ-222](http://oj.haizeix.com/problem/222)：线段树模板(一)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744945401-UAQC581RhOd9rP7S.png)

**样例输入**

```c++
6 5
6 9 4 3 2 1
2 2 5
1 2 3
2 1 6
1 5 12
2 1 6
```
**样例输出**

```c++
9
6
12
```
* 思路
    * 父结点所在区间的最大值可以由子结点得到
        * 【线段树应用场景】父结点的相关信息可以由子结点得到
* 代码
* **① 易理解版 [有l、r]**
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744949943-pZvf4NBJ4Fozr9th.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745095675-Q6tETkIpGHihe2Ld.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745454647-5algtOVfn4ozwPRz.png" alt="图片" style="zoom:80%;" />
    * 使用scanf/printf读入数据或者关闭同步流，否则会超时
    * 查询最大值操作，在缩小查询范围时不能修改查询区间，否则会有扩大查询区间的可能
* **② 优化版 [无l、r]**
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745092096-ZHIomdCS7cYi2FZX.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745090712-2rNvu30M8WuOxdYP.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745456935-UD4dwVA6jBSZYoBG.png" alt="图片" style="zoom:80%;" />
    * 可做**空间**的优化：结点不存储l、r信息
        * 不影响建树过程
        * ⭐修改和查询操作额外添加【当前结点所维护的区间范围】
## [HZOJ-223](http://oj.haizeix.com/problem/223)：线段树模板(二)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745477834-k5aGDdWDalFG5gJf.png)

**样例输入**

```c++
6 5
6 9 4 3 2 1
2 2 5
1 2 3 5
2 1 6
1 5 12 3
2 1 6
```
**样例输出**
```c++
18
35
41
```
* 思路
    * 加入懒标记
    * 注意输出的提示：答案在long long范围内
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744251032-hLGV7N1r0pSs0oGt.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609745452250-VxpSHN4FB2WKg7SH.png" alt="图片" style="zoom:80%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744247588-BBQzGjKVbccL2J0t.png" alt="图片" style="zoom: 80%;" />
    * 一定要区分好【结点维护的区间范围 l ~ r】，以及【查询的区间 x ~ y】
    * [PS] 学会利用flag起到注释调试代码的效果；数据范围为long long
    * ⭐⭐⭐只要遍历就需要下沉，即使是区间修改时
        * 如果区间修改中遍历结点时【不下沉标记】，向上更新可能引发问题
        * 可以尝试测试两种方式下，下面输入的输出
            * ❗ 连续两次区间修改，第二次修改层数**更深**，向上更新时会少考虑懒标记，因为向上更新和值的操作只会看**子树**的和值，默认自己没有懒标记了
            * 遍历时就下沉标记实现更简单，也更易理解
```c++
5 5
1 2 3 4 5
1 1 3 2
1 1 2 2
2 1 3
```
* 结果如下：
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609744244209-KUSgXBQBr9ReT7m8.png" alt="图片" style="zoom:67%;" />
    * 自行画树形图可加深理解
# 思考点

* 好好琢磨下沉标记和向上更新的过程！
# Tips

* 关注口诀，船长归纳得很妙！
* 更多习题可跳转：《面试笔试算法下》——[5 树状数组、线段树练习题](https://doublelll3.ml/mbsx_5_树状数组、线段树练习/)

---


