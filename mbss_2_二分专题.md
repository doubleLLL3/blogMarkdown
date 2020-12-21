---
title: '2 二分专题'
date: 2020-11-01 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
**简单的算法，也有复杂的一面~**

# 二分查找

## ①朴素二分

* 找一个精确对应的值
  
|**两种情况**|**整数**|**浮点数**|
|:----:|:---:|:----:|
|while条件|L <= R|R - L > 0.00001<br>精确到小数点后**4**位|
|***更新参数***| ***更新公式*** | ***更新公式*** |
|mid|(L + R) / 2<br>为了避免溢出👇<br>**((long long) L + R) / 2**<br>**L + (R - L) / 2**|(L + R) / 2|
|L|mid + 1|mid|
|R|mid - 1|mid|

* 浮点数情况放在②、③也成立
## ②⭐特殊二分

* 找0、1的交界处的1
  
|**两种情况**|**000000111111**|**111111100000**|
|:----:|:---:|:----:|
|while条件|L != R 或者 L < R<br>（不能是L <= R，不会有L > R的情况）| 同左 |
| ***更新参数*** | ***更新公式*** | ***更新公式*** |
|mid|(L + R) / 2|(L + R +  1) / 2<br>（避免遇到1 0死循环）|
|L|mid + 1|mid<br>（mid可能是答案）|
|R|mid<br>（mid可能是答案）|mid - 1|

* C++里实际带有实现这个功能的函数
    * lower_bound、upper_bound
    * 参考[关于lower_bound( )和upper_bound( )的常见用法](https://blog.csdn.net/qq_40160605/article/details/80150252)-CSDN
## ③⭐⭐二分答案

* **特殊二分**+***func(答案)***
    * 根据答案才得到判断更新条件的值func(答案)
    * 判断更新条件的值一般隐含着有序的条件
        * 所以是否sort跟func求解方便与否有关，不是必须
# [HZOJ-380](http://oj.haizeix.com/problem/380)：大统领投票（sort）

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/Ywigvx6.png)

**样例输入**

```
3
123456799
123456789132456789123456789
11111111111111
```
**样例输出**
```
2
123456789132456789123456789
```
* 思路
    * 票数很大，要作为字符串读入，使用string类
        * string类重载了＞，自带字典序比较
    * 利用struct类，结合对应的编号
    * 利用sort <algorithm>
        * 先比较长度，strlen
        * 长度一致，则使用字典序
* 代码
    * <img src="https://i.loli.net/2020/11/19/5ndVuBRZmTwyfDe.png" alt="图片" style="zoom:67%;" />
    * const node &a的&是传引用，可以删去，但要加const~
    * 必须判断长度，否则53比12345的字典序大
    * 索引从1开始，否则引入了(0, 0)
# [HZOJ-381](http://oj.haizeix.com/problem/381)：谁拿了最多奖学金（sort）

![图片](https://i.loli.net/2020/11/19/DzeuaXqYcBE7lSs.png)

**样例输入**
```
4
YaoLin 87 82 Y N 0
ChenRuiyi 88 78 N Y 1
LiXin 92 88 N N 0
ZhangQin 83 87 Y N 1
```
**样例输出**
```
ChenRuiyi
9000
28700
```
* 思路
    * 奖金并列第一时，输出最早出现的
        * 所以要记录编号，sort是不稳定的
    * 注意奖金计算规则细节即可
* 代码

* <img src="https://i.loli.net/2020/11/19/sgbeIYajE6TwLPo.png" alt="图片" style="zoom:67%;" />

# [HZOJ-386](http://oj.haizeix.com/problem/386)：吃瓜群众（①）

![图片](https://i.loli.net/2020/11/19/LzDet5AgfikcYRW.png)

**样例输入**

```
5 3
1 3 26 7 15
26 99 3
```
**样例输出**
```
3
0
2
```
* 思路
    * 排序+朴素二分
    * 瓜的数量和编号需揉成struct
* 代码
    * <img src="https://i.loli.net/2020/11/19/1frYZeo3LxjMVXC.png" alt="图片" style="zoom:67%;" />
    * mid计算公式，防止溢出
        * = ((long long)l + r) / 2
        * = l + (r - l) / 2
    * 没找到就输出0，f初始为0
# [HZOJ-387](http://oj.haizeix.com/problem/387)：吃瓜群众升级版（②）

![图片](https://i.loli.net/2020/11/19/HfgjTm2GADeBE16.png)

**样例输入**

```
5 5
1 3 26 7 15
27 10 3 4 2
```
**样例输出**
```
0
5
2
4
2
```
* 思路
    * 抽象为一堆0、一堆1，找第一个为1的情况
    * 考虑输出0的情况
* 代码
    * <img src="https://i.loli.net/2020/11/19/61AGwpVgfcTzCbi.png" alt="图片" style="zoom:67%;" />
    * 有两种处理输出0的情况
    * 000000011111111
# [Leetcode-278](https://leetcode-cn.com/problems/first-bad-version/)：第一个错误的版本（②）

![图片](https://i.loli.net/2020/11/19/zk4LbQV1rc3y7IU.png)

**示例**

```
给定 n = 5，并且 version = 4 是第一个错误的版本。
调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true
所以，4 是第一个错误的版本。 
```
* 思路
    * 用特殊二分查找做
    * 000000011111111
* 代码
```c++
class Solution {
public:
    int firstBadVersion(int n) {
        int l = 1, r = n;  // 版本号从1开始
        while (l != r) {
            int mid = ((long long)l + r) / 2;  // 防止溢出方式一
            // int mid = l + (r - l) / 2;  // 防止溢出方式二
            if (isBadVersion(mid)) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        return l;
    }
};
```
# [HZOJ-390](http://oj.haizeix.com/problem/390)：原木切割（③）

![图片](https://i.loli.net/2020/11/19/MBh7YqU4DsRSKxG.png)

**样例输入**

```
3 8
6
15
22
```
**样例输出**
```
5
```
* 思路
    * 左界→右界：1→max(Xi)
    * 111111110000000
    * ③ = ② + func(mid)
        * func(mid)：根据mid求出可以切出的段数，长度mid是要求的答案
* 代码
    * <img src="https://i.loli.net/2020/11/19/AdEx9BzOKHFV1Y6.png" alt="图片" style="zoom:67%;" />
    * 右界需要确定一下，输入的时候确定
# [HZOJ-389](http://oj.haizeix.com/problem/389)：暴躁的程序猿（③）

![图片](https://i.loli.net/2020/11/19/BUiWopKHxuFa9O4.png)

**样例输入**

```
5 3
1
2
8
4
9
```
**样例输出**
```
3
```
* 思路
    * 距离的上下界：1→max(Xi)
        * 为了简单，直接取**1和最大的工位编号**
        * 如果要精细，可以取最小和最大的工位编号之差
    * 答案→判断依据：距离→可以安排的人数
    * 111111100000
* 代码
    * <img src="https://i.loli.net/2020/11/19/7iXmycZb3nhzN82.png" alt="图片" style="zoom:67%;" />
    * 排序了才好根据距离算可安排人数
    * func()
        * 要记录上一个安排的人的位置last
        * 求可安排的人数s
        * 初始化：可安排人数s为1，上一个安排的人的位置last为num[0]
# [HZOJ-393](http://oj.haizeix.com/problem/393)：切绳子（小数③）

![图片](https://i.loli.net/2020/11/19/Rz7e4wrEovML9mP.png)

**样例输入**

```
4 11
8.02
7.43
4.57
5.39
```
**样例输出**
```
2.00
```
* 思路
    * 类似原木切割，但是数据是浮点数
        * 条件、更新公式不一样
    * 确定查找的左右边界、根据答案得判断依据
* 代码
    * <img src="https://i.loli.net/2020/11/19/P6H7KlDUtGa3wTg.png" alt="图片" style="zoom:67%;" />
    * 浮点数二分查找
    * 直接舍掉小数点后2位后的数字的思路
        * 思路一：*100→转int→除以100.0→设置精度%.2f
        * 思路二：-0.005→设置精度%.2f
        * 供参考：[C++取整：向下取整，向上取整，四舍五入取整，直接去小数点取整](https://blog.csdn.net/Mikchy/article/details/81456057)-CSDN
            * %.2f、<iomanip>都是四舍五入机制
            * 转int：直接去掉小数点及后面的小数取整
# [HZOJ-82](http://oj.haizeix.com/problem/82)：伐木

![图片](https://i.loli.net/2020/11/19/rCJD3cMuSIV4w6b.png)

**样例输入**

```
4 9
20 17 15 10
```
**样例输出**
```
14
```
* 思路
    * 类似原木切割，但判断依据是**切下来的木材**是否满足需要的木材长度
    * 上下界：0→max()
    * 注意数据范围很大，相加有可能溢出，所以使用**long long**
        * **long long要想好位置，如果想不好，可以索性全用**
* 代码
    * <img src="https://i.loli.net/2020/11/19/zNAUMryVKOq7QZj.png" alt="图片" style="zoom:67%;" />
    * 想清楚答案与判断依据之间的关系
# [HZOJ-391](http://oj.haizeix.com/problem/391)：数列分段

![图片](https://i.loli.net/2020/11/19/p4JdEeWYDtgZhCS.png)

**样例输入**

```
5 3
4
2
4
5
1
```
**样例输出**
```
6
```
* 思路
    * 最小的最大和
    * 0000000111111，找第一个1
        * 答案（段数和）mid小→判断依据（段数）s多，不满足条件，对应0
        * 答案（段数和）mid大→判断依据（段数）s少，算是满足条件，对应1
        * 因为找的是最小的最大和，考虑交界处
        * ❓❗如果是找最大的最大和，就会变成111110000情况
    * 左右界：max(Ai)→sum(Ai)
* 代码
    * <img src="https://i.loli.net/2020/11/19/siTmGMgLZlrU6C3.png" alt="图片" style="zoom:67%;" />
    * <img src="https://i.loli.net/2020/11/19/mEQfzuJoe7OgM9L.png" alt="图片" style="zoom:67%;" />
    * ⭐func函数好好琢磨！
    * 注意long long类型
# [HZOJ-394](http://oj.haizeix.com/problem/394)：跳石头

![图片](https://i.loli.net/2020/11/19/PYf2LdeIsqi4hly.png)

**样例输入**

```
25 5 2 
2
11
14
17 
21
```
**样例输出**
```
4
```
* 思路
    * 最短跳跃距离的最大值
    * 起点和终点隐含存在：min(D(i+1) - Di)→L（起点到终点的距离）
    * 111110000
        * 因为找的是最大值，越往右值越大，所以找最后一个1
* 代码
    * <img src="https://i.loli.net/2020/11/19/GF1h8orORXuBkW3.png" alt="图片" style="zoom: 67%;" />
    * 要记得存**起点和终点**！
    * 对遍历到终点石头但不会移走终点石头的思考
        * 可以用移走前一块石头等效
# [HZOJ-392](http://oj.haizeix.com/problem/392)：丢瓶盖

![图片](https://i.loli.net/2020/11/19/bWU8PNo7hDZHtCK.png)

**样例输入**

```
5 3
1
2
3
4
5
```
**样例输出**
```
2
```
* 思路
    * 与上述暴躁的程序猿一模一样！
* 代码
# [HZOJ-395](http://oj.haizeix.com/problem/395)：复制书稿

![图片](https://i.loli.net/2020/11/19/UR286XNkKMEmPzO.png)

**样例输入**

```
9 3
1 2 3 4 5 6 7 8 9
```
**样例输出**
```
1 5
6 7
8 9
```
* 思路
    * 每个人的抄书速度都一样，数字就对应时间
    * 必须连续，书不能拆分
    * 步骤
        * 第一步：求最大值，与上述的数列分段一模一样！
        * 第二步：每个人抄书的起止编号，存在数组里
            * 拿到最大值后，**从后往前**扫数列
                * 如果有多解，让前面的人少抄写
            * 稍有麻烦，考察代码功底
* 代码

---


# 附加知识点

* C++里的struct为类
    * 成员访问权限默认**私有**，而struct默认公有
* sort \<algorithm\> 使用
```
int num[105];
sort(num, num + n, cmp); 
```
* 
    * num：要排序的区间首地址；num + n：区间尾地址的下一地址；cmp：
    * ⭐默认是不稳定的
        * 底层使用快速排序，结合插入排序和堆排序
        * 参考[C++一道深坑面试题：STL里sort算法用的是什么排序算法？](https://zhuanlan.zhihu.com/p/36274119)-知乎
    * 对于int、string：可以直接比较大小，默认从小到大排序
    * 对于自定义结构
        * 重载小于号
        * 自己写排序方法cmp
```
bool cmp(node a, node b) {
  return a.x > b.x;  // 按照node里的x从大到小排序
}
```
* <img src="https://i.loli.net/2020/11/19/UTRP5WrcqmQLOIx.png" alt="图片" style="zoom: 50%;" />

# 思考点

# Tips

---


# 课程速记

* 
