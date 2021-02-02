---
title: '1 二叉排序树、AVL树'
date: 2020-12-19 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
数据结构，就是定义一种性质，并且维护这种性质

*BS树👉AVL树*

# 课程内容

## 二叉排序树

[又叫二叉搜索树、二叉查找树]

### 基础知识

* 性质：对于任意一个三元组，左子树 < 根结点 < 右子树
* 用途：解决与**查找**、**排名**相关的检索需求
* 【为什么树形结构的查找入口一定是根结点？】
    * 首先明白点和边的含义：集合和关系
    * 而根结点代表全集
* 结构操作：增删查
    * 增
        * 不断与子树的根结点比较，递归，直到子树没有子结点
        * ⭐新结点一定会成为叶结点
    * 删 [结点]
        * 度为0：直接删除
        * 度为1：把子树[孤儿子树]直接挂到其父结点上
        * 度为2：[稍麻烦]
            * 找到前驱或者后继替换原结点
                * 前驱结点：其左子树中最大值对应的结点 [一定没有右子树，度最多为1]
                * 后继结点：其右子树中最小值对应的结点 [一定没有左子树，度最多为1]
                * 【只针对度为2的结点，否则需考虑往父结点找、找到什么程度？】
            * ⭐从而转换为删除度为0或1的结点 [前驱或者后继] 问题
            * [亦可理解为一维数组的删除操作]
    * 查：根据性质递归查找即可
* 插入顺序会影响树形结构，对应不同的平均查找效率
    * 同样的序列，但不同的插入顺序
    * 平均查找效率：结点查找次数的期望值。即平均查找次数：查找总次数 / 结点数
        * 假设每个结点等概率地被查找
* 中序遍历→从小到大的有序序列
* 可参考[《基础数据结构》——3 树与二叉树——二叉搜索树](https://doublelll3.ml/jsj_3_%E6%A0%91%E4%B8%8E%E4%BA%8C%E5%8F%89%E6%A0%91/#%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91)
### 扩展内容

[具体代码见代码演示——二叉排序树]

* 删除操作优化 [代码演示——二叉排序树]
    * 度为0和度为1的结点删除操作可以共用【度为1】的删除操作代码
    * 度为0的代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813661558-2rAVU2oCNlmVIkI8.png" alt="图片" style="zoom:67%;" />
    * 度为1的代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813660151-nGJ0n24r68kiq01J.png" alt="图片" style="zoom:67%;" />
    * 当度为0时，temp指向NULL，同样销毁root，然后返回NULL
* <排名问题>——找排名**第**k位的元素
    * ⭐增加size字段，记录每棵子树的结点数量 [包括根结点]
        * 数据结构的精髓，针对具体问题【修改结构定义】
        * 插入、删除时要更新size
    * 查找条件
        * ① k = LS + 1，根结点就是咱们要找的值
        * ② k < LS + 1，排名第k位的元素在左子树中，到左子树中继续找
        * ③ k > LS + 1，排名第k位的元素在右子树中，到右子树中找排名第k - LS - 1位的元素
        * [PS] LS即左子树的结点数量
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813657803-CHfP6yiSrOSJtrKx.png" alt="图片" style="zoom:67%;" />
        * 在边界条件处输出 [-1或key]
* <Top-k 问题>——找排名**前**k位的元素 [排名<=k的所有元素]
    * 【基于排名问题】
    * 查找条件
        * ① k = LS + 1，把左子树中的值全部输出
        * ② k < LS + 1，前k位元素全都在左子树中
        * ③ k > LS + 1，根结点和左子树中的元素，都属于前k位元素
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813656683-BYkj73QILaoxyhf6.png" alt="图片" style="zoom:67%;" />
        * 相当于一直缩小树的大小，从而转变成第一种情况
        * 解法其实很多 [比如堆]，没有标准答案
* 二叉排序树和快速排序的关系
    * 【二叉排序树是快速排序在思维结构层面用的数据结构】
    * 快速排序中的Partition操作
        * 把基准值看成二叉排序树的根结点
        * 每一次Partition操作后，基准值和左、右两边的关系其实就是👇
        * 根结点和左、右子树的关系
    * ⭐弄懂下面两个思考即可理解两者关系
        * 思考1：快排的时间复杂度和二叉排序树的建树时间复杂度之间的关系
            * 其实是一码事，建树过程类似多个Partirion过程
        * 思考2：快速选择算法和二叉排序树之间的关系
            * 两者本质也一样，前者表现为算法，后者表现为数据结构
        * [个人理解]
            * 快速排序是**一堆数**先做一次Partition，分半后继续
            * 建树过程则是**一个数一个数**地Partition，每次都定好这个数地位置再走下一个
## 平衡二叉查找树—AVL树

解决二叉查找树中的退化问题

### 基础知识

* 背景
    * 发明者：AV、L，AV的贡献更大
    * 年代：1962年
    * [神经网络也诞生于这个年代]
* 性质
    * 本质上也是二叉排序树，拥有二叉排序树的所有性质
    * 添加性质：左、右子树的高度差不超过1 [左、右子树差不多大]
* ⭐平衡条件、平衡调整
### [进一步]思考

高度为H的BS、AVL树，所包含的结点数量在什么范围？

* [二叉树通用上限：2 ^ H - 1 ]
* <BS>    H ~ 2 ^ H - 1
* <AVL>  low(H - 2) + low(H - 1) + 1 ~ 2 ^ H - 1
    * 即1.5 ^ H ~ 2 ^ H - 1，low(H)代表高度为H的AVL树的最少结点数
    * 左边界是一个斐波那契数列
        * 其增长速度为1.618 ^ n ≈ 1.5 ^ n 【可用来预估】
    * 👉结点数量和高度之间的关系为对数关系，所以查找效率是O(logN)级别
* ❓ [进一步] 普通的二叉排序树的查找效率一定比AVL树差吗？
    * 不一定，取决于插入序列的顺序，但是AVL树的**下限高**
    * 【引申】
        * 树高 = 生命长度，结点数量 = 生命财富，不同的算法，结果不一样
        * 教育提高的是底线，而不是上限，上限取决于能力和运气
            * 上限很大程度取决于一个不可选择的插入序列，听天由命
### 调整⭐

插入/删除 + 调整：插入操作与二叉排序树一致，关键在于调整 [⭐抓着哪个结点旋]

#### 旋转

[失衡的时候使用的基本操作]

* 左旋
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813653510-7rWcUV22W0nBjh0y.png" alt="图片" style="zoom: 50%;" />
    * 抓着根结点，以中心点向左旋转
        * K3变成根结点
        * K1成为K3的左子树
        * K3原本的左子树A成为K1的右子树 [因为K3 > A > K1]
    * "叶结点"深度变化：A不变，B - 1，K2 + 1
        * 👉子树树高变化：左子树 + 1，右子树 - 1
* 右旋
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813651811-ejsL1Ot7KGHe5fZx.png" alt="图片" style="zoom:50%;" />
    * 抓着根结点，以中心点向右旋转
        * K2变成根结点
        * K1成为K2的右子树
        * K2原本的右子树B成为K1的左子树 [因为K2 < B < K1]
    * "叶结点"深度变化：A - 1，B不变，K3 + 1
        * 👉子树树高变化：左子树 - 1，右子树 + 1
* 左、右旋是对称操作，本质上影响的是子树的树高
#### 失衡类型 && 调整策略

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813650472-0oTk8RyS96IeDjMt.png" alt="图片" style="zoom:50%;" />
* 判断场景：回溯过程中，**第一次**发现失衡时，只要失衡就调整
* 情况对称：LL ——RR ，LR——RL

**① LL**

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813647830-H6Dpp7yyZBDetsFg.png" alt="图片" style="zoom: 50%;" />
* ⭐关键关键⭐：分析h1、h2、h3、h4之间的关系 [写等式]
    * 分析
        * 一个一个插入，新插入的结点一定是到A子树导致的LL型失衡  [删，也是一个一个删]
        * 插入前，一定是平衡的
        * 插入后，K1的左子树比右子树高出2
            * 即 h(K2) = h(K3) + 2，而
                * h(K2) = h1 + 1
                * h(K3) = max(h3, h4) + 1
    * 等式关系=>
        * h1 = max(h3, h4) + 2 = h2 + 1
        * [PS] |h3 - h4| <= 1
* **<调整策略>**大右旋。结果见上图，平衡分析见下：
    * ① K1：左子树高度`h2`= 右子树高度`max(h3, h4) + 1`=`h1 -1`
    * ② K2：左子树高度`h1`= 右子树高度`h2 + 1`
    * 已平衡，且可以说是平衡得可怕

**② LR**

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813645235-HKt6yYK0k5Z8cU3c.png" alt="图片" style="zoom:50%;" />
* ⭐关键关键⭐：分析h1、h2、h3、h4之间的关系 [写等式]
    * 分析
        * 一个一个插入，新插入的结点一定是到B / C子树导致的LR型失衡
        * 插入前，一定是平衡的
        * 插入后，K1的左子树比右子树高出2
            * 即h(K2) = h(D) + 2，而
                * h(K2) = max(h2, h3) + 2
                * h(D)  = h4
    * 等式关系=>
        * max(h2, h3) = h4 = h1
        * [PS] |h2 - h3| <= 1

❗ 再次切记！判断始终发生在回溯过程中**第一次**失衡时，所以在此之前的所有子树都是平衡的

* **<调整策略>**小左旋 + 大右旋。结果见下图：
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813643020-2L3JY3X7DcgD3HRP.png" alt="图片" style="zoom:50%;" />
    * 先小左旋转换成LL型，再大右旋 [LL调整策略]
    * 平衡分析
        * 很明显，左右子树的高度取决于h1、h2、h3、h4
        * 而它们之间高度差不超过1 [h2或h3中的一个可能小1]
#### 调整策略小结

* 发生在回溯阶段的，第一个失衡结点处
* 调整关键：四种情况下，ABCD四棵子树的树高关系
* 四种情况
    * LL、LR：最后都需要大右旋
        * LL，大右旋
        * LR，先小左旋，再大右旋
    * RL、RR：最后都需要大左旋
        * RL，先小右旋，再大左旋
        * RR，大左旋
## [平衡二叉查找树—SBTree]

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813640801-eSl5PBZ854sawUbP.png" alt="图片" style="zoom: 50%;" />
* AVL通过树高进行平衡，而SBTree是通过结点数量进行平衡
* 参考AVL的学习顺序：平衡条件 + 平衡调整 [插入、删除]
# 随堂练习

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813638499-WRjtxDnyIMeQQTmF.png" alt="图片" style="zoom: 67%;" />
    * 第二个序列组成的二叉搜索树退化成了一个链表，插入顺序会影响结构
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813637515-1nH5c5reFT1t1nx9.png" alt="图片" style="zoom: 67%;" />
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813636654-NloTmld9BSZv8CZd.png" alt="图片" style="zoom:50%;" />
    * 判断失衡时，从插入结点往上一步一步回溯，有失衡就马上调整
# 代码演示

## 二叉排序树

* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813634947-QSgGujqAnE912iRq.png)
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813633796-xujr8quH2ViEjAct.png)
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813632467-TTbiVtWjksKvLnpw.png)
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813631212-TP19R3E4OlziXKOQ.png)
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813629833-J4R3aCglLt6jQfQk.png)
* 5种基本操作：初始化、销毁、查找、插入、删除
* 增加排序问题[增加size字段]、Top-k问题解答[基于排序问题]
* [PS]
    * 找前驱存在bug：度为1或0的结点的前驱不一定存在于子树中，但此场景不用管
    * 递归必须考虑边界条件
    * 学会用宏让代码更美观
    * 分析二叉树基本都是讨论三种情况：左、根、右
* 部分结果
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813627887-VlDPkMFxrYHiZDKm.png" alt="图片" style="zoom: 67%;" />

【附】随机生成操作的代码

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813626406-CWAWcvzHaCmcMkhP.png" alt="图片" style="zoom:67%;" />
* 生成非等比例的操作类型
## AVL树

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813624616-iwSYZYrN7yk2bHx1.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813622929-spSMNXTqWRA8u9BT.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813620752-lU1JgTt5F3oviyw6.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608813617665-F3oLDbWKi4SfXWJd.png)

* 插入和删除后，注意重新计算树高【时刻记得维护结构定义】
    * 插入 / 删除结点时 + 左旋 / 右旋调整时
* ⭐引入了NIL结点，代替NULL🆒[红黑树也需要用到]
    * NULL不可访问
    * NIL是一个实际结点，可以访问
    * 【方便代码实现】
* LL、LR以及RL、RR最后一个操作分别都是大右旋以及大左旋
# 随堂练习

## [HZOJ-xxx](http://oj.haizeix.com/problem/590)：xxx

**样例输入**

```c++

```
**样例输出**
```c++

```
* 思路
* 代码
# 附加知识点

* 分析二叉树情况时，基本都是分三种情况：左、根、右
# 思考点

* 普通的二叉排序树的查找效率一定比AVL树差吗？
    * 不一定，取决于插入序列的顺序，但是AVL树的**下限高**
    * 【引申】
        * 树高 = 生命长度，结点数量 = 生命财富，不同的算法，结果不一样
        * 教育提高的是底线，而不是上限，上限取决于能力和运气
            * 上限很大程度取决于一个不可选择的插入序列，听天由命
# Tips

* 学C++的前提：系统编程、网络知识、算法结构、C语言
* 所谓算法设计及分析能力：分类讨论 [分几种情况] 及归纳总结 [多种情况合为一种] 的能力
    * 程序 = 算法 + 数据结构
    * 通过刷题提高该种能力的人是天赋型选手
* 推荐极客专栏——[人人都能学会的编程入门课](https://time.geekbang.org/column/intro/268)——向传统学习方法发起的挑战
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1608811203861-1sljNOfzd5A8C5gA.png" alt="图片" style="zoom: 50%;" />

---


