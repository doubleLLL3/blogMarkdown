---
title: '3 哈夫曼树与哈夫曼编码'
date: 2020-12-26 14:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
直观了解哈夫曼树和哈夫曼编码，证明哈夫曼编码是最优的变长编码

# 课程内容

## 前置知识

* 什么是编码
    * 最先听到编码是在什么地方？ASCII码
        * 'a' = (97)10 = (0110 0001)2
        * '0' = (48)10 = (0011 0000)2
        * 8位二进制编码，一字节
    * 注意：在计算机中，任何信息都是用二进制存储的
    * 编码的作用：人类字符到计算机字符的映射
* 举例：编码长度带来的影响
    * 计算机中有一段信息："aa00"，其**ASCII编码**为：01100001 01100001 00110000 00110000
    * 此时，将该信息从一台计算机传输到另外一台计算机
        * 在网络中，最小的传输单位是比特位 [bit]，所以"aa00"需要传输32个bit
    * 假设：某计算机的网速是32bit/s，则用时为：1s
    * 👇
    * 转换到特殊场景：只有a、b、0、1四种字符需要传输
    * 则可设计新的编码方式 [海贼班编码-定长编码]
        * 用2位二进制区分它们——a: 00，b: 01，0: 10，1: 11
    * 此时，"aa00"的表示只需要8个bit
    * 所以，在网速不变的情况下，当前传输的用时**只需**：0.25s
    * [PS] 相比腾讯会议，Zoom的编码解码更精细化，所以相同网速下Zoom体验更好
## 定长与变长编码

* 定长编码——对于每个字符，编码长度相同
    * ASCII码和特定场景下的海贼班编码，都属于定长编码
    * [PS] UTF-8—变长编码；UTF-16—是定长编码 [自学]
* 变长编码——对于每个字符，编码长度不相同
* 变长编码，一定不差于定长编码
    * 定长编码，是变长编码的特例
* 【引申理解】
    * 变长编码可以就是定长编码
    * 变长编码需针对于特定场景做优化 [传输字符的出现概率是需要提前统计好的]
    * 编码可以看成是一种协议，提前规定好了，而不能是动态变化的
    * 编解码需保持一致
## 变长编码的应用场景

* 特定场景
    * 只有四种字符：ab01
    * 每个字符出现的概率不同——a: 0.8，b: 0.05；0: 0.1；1: 0.05
* 平均编码长度
    * avg(l) = ∑ li × pi
    * li：第 i 种字符的，编码长度
    * pi：第 i 种字符的，出现概率
    * 实际上就是编码长度的期望值
    * 举例
        * 假设平均编码长度为1.16，则传输100个字符，需要传输116个比特位 [估算]
        * 海贼班编码 [见上] 的平均编码长度：avg(l) = 2 × ∑ pi = 2
            * 定长编码的平均编码长度就是定长
* 新 · 海贼班编码
    * a：1
    * b：01 [注意：不能以1开头，因为**解码**时会和a冲突，❌前缀重叠]
    * 0：000
    * 1：001
    * 此时，平均编码长度：1 * 0.8 + 2 * 0.05 + 3 * 0.1 + 3 * 0.05 = 1.35
    * 所以，传输100个字符，只需传输135个比特位 [估算]
* [PS] 概率越大的字符对应越短的编码长度
## 哈夫曼编码

1. 先统计得到每一种字符的概率
2. 将n个字符，建立成一棵哈夫曼树
3. 每一个字符都落在叶子结点上 [不会前缀重叠]
4. 按照左0、右1的形式，将编码读取出来
* 建树举例
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743939765-yau7obDXQIkCERiw.png" alt="图片" style="zoom:67%;" />
    * 建树过程口诀：每次拿出概率最小的两个字符作为结点，合成一棵子树 [产生新的结点]
    * ⭐因为所有字符都落在叶子结点上，所以没有任何一个编码是其它字符的编码前缀
        * 没有冲突
    * 此时，平均编码长度位为1 * 0.8 + 3 * 0.05 + 2 * 0.1 + 3 * 0.05 = 1.3
* 结论：哈夫曼编码是最优的变长编码 [下面给出证明]
* [PS]
    * 主要关注长度，01的左右不太关注
        * 若要关注，则考虑0、1的传输成本谁更高
    * 哈夫曼树不唯一，概率相等的时候顺序随意
    * 哈夫曼编码也可退化为定长编码 [同理，比如编码4个等概率(0.25)的字符]
## 证明哈夫曼最优

[变长编码中的大哥，最优指的是平均编码长度最小]

步骤：① 转换平均编码长度的表示；② 求解公式最优解

### 思维转换

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743937129-yOCZkTd4rIZcDtRR.png" alt="图片" style="zoom:67%;" />
* 对于哈夫曼编码过程，如果红色结点对应了一个字符，则下面的4个叶子结点都要被砍掉
* 否则，就会发生冲突
* [从直觉上，也是事实] 哈夫曼编码会覆盖所有的叶子结点
* 假设有4个字符，其编码结果如下 [红色结点]
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743935720-0UTPI8rC20jHaWkj.png" alt="图片" style="zoom:67%;" />
    * 可知，高度为H [从0开始] 的树，第l层的结点覆盖的叶子结点数为2 ^ (H - l)
        * 第l层的l其实代表的就是编码长度
        * 越往上的结点覆盖的结点数量越多，越往下的反之
* 👇
* 第i个字符的编码长度 [左] 和对应结点所覆盖的叶子结点数 [右] 之间存在**映射**，如下：
    * li → 2 ^ (H - li)
### 隐含的约束条件

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743934222-koro8z3is6WGrYDg.png" alt="图片" style="zoom:67%;" />
* 对第二个式子的左右两边，同时除以2 ^ H
* [PS] 哈夫曼树的第二行式子一定是等号，但先不要着急
### 证明目标转换

* 使平均编码长度 Σ pi * li 最小，li = -li'
* 约束来自于上面的树形结构 [即隐含的约束条件]
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743932472-Wl6gQroXCxo3tccd.png" alt="图片" style="zoom:67%;" />
* 再令Ⅰi = 2 ^ li'
* 即让目标和约束都再做转换
* 【转换结果】
    * 目标：使 -(p1logⅠ1 + p2logⅠ2 + p3logⅠ3 + ... + pnlogⅠn) 最小
    * 约束：Ⅰ1 + Ⅰ2 + Ⅰ3 + ... + Ⅰn <= 1
* [PS]
    * 有点像熵？其实就是从熵的角度证明，还可以引出交叉熵的概念
    * 转换是为了什么？
        * 推导出约束条件的等号成立与目标最小的关系
        * 还有一个限制条件：概率和为1
### 约束条件再缩小

* 证明：当目标函数达到最小时，约束条件一定=1
* 反证法：即约束条件<1
    * 令1 - ∑Ⅰi = Ⅰx'，其不为负 [看约束条件]
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743930481-OAPgKGhhXaYKreM1.png" alt="图片" style="zoom:67%;" />
    * Ⅰx'可以加到任意项上
    * 只要Ⅰx'不为0，那么目标函数就一定不是最小，还能小
    * 所以约束条件一定=1
### 求偏导等于0得极值

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743929015-zouKkO3dl3WTHVZS.png" alt="图片" style="zoom:67%;" />
* 减少一个未知量：Ⅰn = 1 - Ⅱ
* 什么时候目标达到最小值：求偏导
    * 求偏导可得左边式子
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743926729-VlJfjAwyVwbQzGrL.png" alt="图片" style="zoom: 67%;" />
    * 即得右上角式子，其成立时，目标可以达到最小值
    * 令p1/Ⅰ1 = ... = k，又已知约束条件和概率和为1得两个条件，可得k = 1
    * ⭐所以pi = Ⅰi
### ❗ 思考

平均编码长度与熵、交叉熵的关系

* pi = Ⅰi
    * Ⅰi与编码长度li相关，也就是概率和长度密切相关
        * 概率越大，编码长度越短
    * 将等式代入，可得【**熵**】的公式
        * 其实，熵代表的是在一个系统中，要表示一些信息，所需的**最小**的平均表示单元
        * 同样，也可以理解为平均编码长度 [⭐思想连通]
        * 平均编码长度越长 → 系统中的字符数量越多 → 系统的状态越多 → 系统越混乱
        * 熵越大代表系统的编码越混乱
    * **【交叉熵】**
        * 把pi和Ⅰi都看成是一组概率
        * 其描述的是两个概率向量的相似程度
* 其实证明还不是很严谨
    * 因为哈夫曼过程其实是离散的，而此证明过程是连续的
    * 如：pi = Ⅰi，通过Ⅰi算出的编码长度li可能是小数，但编码长度li实际上肯定是整数
# 代码演示

## 哈夫曼树

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743921719-yr3uQ4p2Mz4fE1oE.png" alt="图片" style="zoom:80%;" />
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743921321-m6csL2yqJMPhk2mU.png" alt="图片" style="zoom:80%;" />
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609743921321-3689vX5wdpMLGpIt.png" alt="图片" style="zoom:80%;" />
* 结构基于树
* 【关键操作】
    * 合并结点，可建立结点之间的关系 [数组与结点关系之间的转换]
    * 找最小的2个概率 [可利用优先队列优化]
    * 递归提取编码 [只有叶结点才对应字符编码]
    * 读入数据时建议使用字符数组存储字符，可减少出错率
# 附加知识点

* 计算机中，最基本 [最小] 的存储单位是字节，最基本 [最小] 的数据表示单位是bit位
# 思考点

* 哈夫曼是最优编码的其它证明方式？
    * [058哈夫曼算法的正确性证明](https://www.coursera.org/lecture/algorithms/058ha-fu-man-suan-fa-de-zheng-que-xing-zheng-ming-nLQya)——Coursera
    * [最简单的哈夫曼树是最优二叉树证明方法](https://zhuanlan.zhihu.com/p/42682935)——知乎
# Tips

* 数学知识决定计算机方向的上限

---


