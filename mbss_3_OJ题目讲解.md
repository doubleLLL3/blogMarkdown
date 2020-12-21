---
title: '3 OJ题目讲解'
date: 2020-11-04 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# [HZOJ-599](http://oj.haizeix.com/problem/599)：两数之和1

![图片](https://i.loli.net/2020/11/19/BmI8NJYze9LuAX3.png)

**样例输入**

```
6 15
1 5 6 7 10 26
```
**样例输出**
```
1 4
```
* 思路
  
|**方法**|**说明**|**时间复杂度**|**空间复杂度**|
|:----:|:---:|:----:|:---:|
|暴力|两层循环|O(N^2)|O(1)|
| 暴力 |固定一个数，<br>另一数用二分去查找|O(NlogN)|O(1)|
|哈希表|遍历第一遍，存<br>遍历第二遍，找|O(N)|O(N)|
|⭐双指针|两边指针加，<br>不断更新|O(N)|O(1)|

* 双指针
* 数组有序
* 同理，三数之和、四数之和...
    * 多一层循环
* 代码

* <img src="https://i.loli.net/2020/11/19/Otkn4bwpS6iG32a.png" alt="图片" style="zoom:67%;" />

# [HZOJ-600](http://oj.haizeix.com/problem/600)：杨氏矩阵

![图片](https://i.loli.net/2020/11/19/g5tGNX2AIwvmHoi.png)

**样例输入**

```
3 4
1 2 3 4
5 6 15 20
7 10 20 25
15
```
**样例输出**
```
2 3
```
* 思路
    * 从左下角出发或从右上角开始
        * 逐步移动横坐标或纵坐标一个单位
        * 时间复杂度：O(n + m)
    * 和上一题的双指针思想很相似~
        * <img src="https://i.loli.net/2020/11/19/9XlsgpfSCcUE8Kw.png" alt="图片" style="zoom:67%;" />
        * 二维空间和一维空间的转换！
* 代码
    * <img src="https://i.loli.net/2020/11/19/BPUfbuiI9Cejpmg.png" alt="图片" style="zoom:67%;" />
    * 提交还是有2个test超时，还有更快的方法吗？
        * 没有，是OJ平台的波动性导致的

---


# [HZOJ-477](http://oj.haizeix.com/problem/477)：元音字母

![图片](https://i.loli.net/2020/11/19/zpktXPSGFxnNVvu.png)

**样例输入**

```
ABCDEFGIKJUMNBZZ
```
**样例输出**
```
44
```
* 思路
    * 找到上一个元音字母的位置
    * 遍历接下来的元音字母的位置
    * 计算距离，更新答案
* 代码
    * <img src="https://i.loli.net/2020/11/19/Iflu1rka3BQ5syg.png" alt="图片" style="zoom:67%;" />
    * s[i]为\0时停止
    * last初始为-1，可以在找到第一个元音时，不计算距离，只更新last
# [ZOJ-479](http://oj.haizeix.com/problem/479)：乒乓球

![图片](https://i.loli.net/2020/11/19/XLuGDeBpxOI3JN8.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/4ICcDh9.png)

**样例输入**

```
WWWWWWWWWWWWWWWWWWWW
WWLWE
```
**样例输出**
```
11:0
11:0
1:1
21:0
2:1
```
* 思路
    * 给一个计分结果，分别输出11分制下和21分制下的比赛结果
        * 输出每局得分几比几
    * 根据输入数据每行最多25个字母，最多有2500行
        * 得到2500 * 25分，/11或者/21
        * 得到11分制下最多**6000**局，21分制下最多**3000**局
        * 判断要开的数组大小
* 代码
    * <img src="https://i.loli.net/2020/11/19/WvYqlIMtJA1hScs.png" alt="图片" style="zoom:67%;" />
    * cin读入规则
        * 遇到空格、换行符和制表符结束输入，并在缓冲区中留下使输入结束的结束符(Enter、Space、Tab)，作为下次cin>>开头的忽略
        * 遇到EOF会返回1
# [HZOJ-480](http://oj.haizeix.com/problem/480)：保龄球

![图片](https://i.loli.net/2020/11/19/dgX78LmuoRjGqpQ.png)

**样例输入**

```
/ / / 72 9/ 81 8/ / 9/ / 8/
```
**样例输出**
```
192
```
* 思路
    * 关键就是红框那段话
    * **三种计分情况**
        * 直接清空：本轮10分+接下俩2球得分
        * 间接清空：本轮10分+接下俩1球得分
        * 没清空：本轮得分
    * 一局十轮
    * 👌使用结构体
        * 字符数组：每轮对应的得分字符串  8/
        * 第一次后得分
        * 第二次后得分：本轮最终得分，这里很巧妙，根据题意设置
        * Flag：属于上述第几种情况
* 代码
    * <img src="https://i.loli.net/2020/11/19/MkxfRD6ya2IAcOC.png" alt="图片" style="zoom:80%;" />
    * 关键在理解规则，以及巧妙利用结构体！
    * s数组开4，本来最多是2个字符+\0，但是因为字节对齐，开3开4一样，这里开4
    * 在终端需使用ctrl + d结束cin所在的for循环
# [HZOJ-481](http://oj.haizeix.com/problem/481)：冰壶比赛

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/L1zejhr.png)

**样例输入**

```
8
5 20 18 19 3 15 13 3
20 2 17 12 5 18 10 11
20 3 4 1 2 11 9 2
1 15 19 9 8 14 11 10
15 2 10 1 19 14 3 18
15 17 21 19 24 32 19 26
-1
```
**样例输出**
```
0:1
0:0
3:0
3:1
```
* 思路
    * 谁能得分：所有冰壶离圆心最近的队可以得分
    * 得多少分：在半径r内，且在对方离圆心最近的冰壶构成的⚪内，得分队有多少个冰壶
        * <img src="https://i.loli.net/2020/11/19/aMHw8QJvCE2b6Ak.png" alt="图片" style="zoom: 50%;" />
        * 绿队可得2分
        * 来自田船长之笔~
    * 最多10局，20行+1
    * 先输出每轮的比分，再输出总比分
* 代码
    * <img src="https://i.loli.net/2020/11/19/Kt1biSJAzFjTe54.png" alt="图片" style="zoom:80%;" />
    * 利用sort更方便找获胜方和计分，消耗不大
# [HZOJ-484](http://oj.haizeix.com/problem/484)：柱状统计图

![图片](https://i.loli.net/2020/11/19/sXDObxmiRZdfyFk.png)

**样例输入**

```
ABC ABC.DEF()G GCC XY
354342aaaCaa aaaaaaaabcdbcd
```
**样例输出**
```
    *
    *
    *
* * *       *
* * * * * * *                                 * *
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
```
* 思路
    * 简单：统计**大写字母**的个数
    * **难点**：每行不要有多余的空格；按列输出
    * 流程
        * 需根据最大的字符出现次数统计行数
        * 外层循环：从mmax到1，在满足条件的字符位置上输出 *
            * 先得到每行最后一个输出 * 的字符，从Z到A判断是否满足条件
        * 里层循环：从A到ind，需统计每一行最后一个字符是哪个
    * 注意*和空格的输出条件
        * 就是判断字符的统计数是否大于在该行输出*需满足的数量
* 代码
    * <img src="https://i.loli.net/2020/11/19/58fc9JABHlxWtTs.png" alt="图片" style="zoom:80%;" />
    * 逻辑要理清~
    * num数组开130位很巧妙，与ASICII码范围对应，统计时不需要判断
    * 首位判断：避免有多余的空格
# [HZOJ-485](http://oj.haizeix.com/problem/485)：均分纸牌

![图片](https://i.loli.net/2020/11/19/z49Zh75avNF3XqR.png)

**样例输入**

```
4
9 8 17 6
```
**样例输出**
```
3
```
* 思路
    * 只能移到相邻牌堆
    * 先算平均数，得到每堆牌的预期数量
    * 从一个方向凑到平均数即可
        * 从左往右
        * 不够借，右边变负数也没问题
        * 本质和两种方向凑一样！
* 代码
    * <img src="https://i.loli.net/2020/11/19/TnwcriY7EAZbNxh.png" alt="图片" style="zoom:80%;" />
    * 最后一堆牌不用考虑
    * 只需更新右边一个数，公式无论加减都成立
# [HZOJ-503](http://oj.haizeix.com/problem/503)：独木舟（自己做）

![图片](https://i.loli.net/2020/11/19/kPYT8jNq397Jn4B.png)

**样例输入**

```
100
9
90
20
20
30
50
60
70
80
90
```
**样例输出**
```
6
```
* 思路
    * 排序再遍历判断
* 代码
# [HZOJ-504](http://oj.haizeix.com/problem/504)：删数⭐

![图片](https://i.loli.net/2020/11/19/giwp68SHrkbcxEl.png)

**样例输入**

```
179566
4
```
**样例输出**
```
15
```
* 思路
    * 大整数
    * **精髓**：越靠前的数越小，整体才会小
        * 大数在前、小数在后情况，删掉大数
        * 而直接删除大数是不行的
        * 单调栈知识？参考[什么是单调栈？](https://www.cxyxiaowu.com/450.html)-五分钟学算法
    * 流程：
        * 扫一下大整数，得到字符串
            * **使用string类**，可以比较方便地删数
            * string.replace()
        * 遍历每两位
        * 循环删的次数s次
        * 输出
            * 忽略前导0，必须要有flag，只是判断前导0，不省略其他0
    * 延伸：删字母使字典序尽可能小
* 代码
    * <img src="https://i.loli.net/2020/11/19/AiYZz9nvrJtBdy6.png" alt="图片" style="zoom:80%;" />
    * ind默认为最后一位！即最大数，因为是从小到大排列的
    * str.replace操作，参考[std::string::replace](http://www.cplusplus.com/reference/string/string/replace/)-cplusplus
    * 处理前导0操作：定义flag
# [HZOJ-505](http://oj.haizeix.com/problem/505)：最大整数

![图片](https://i.loli.net/2020/11/19/5jUq2k4x7aiNI8e.png)

**样例输入**

```
3
121 12 96
```
**样例输出**
```
9612121
```
* 思路
    * ⭐保证字符串连接后的字典序最大
        * 保证任意2个元素的连接，都是前面的字典序大于后面的
        * 使用sort
    * 不行的方法
        * 按字典序排序  121 12 96 👉 9612112
        * 高几位相同判断长度，短的放前 129 12 96 👉 9612129
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/cd1NalP.png" alt="图片" style="zoom:80%;" />
    * **string类**可以用+自动连接
        * 以字符串形式读入
    * ❗把>改成>=在oj里第二个test会超时
        * 两者区别在于=的情况返回true还是false，和sort排序本身不稳定没有关系~
        * ⭐cmp函数不简单
            * 必须满足Strict Weak Ordering
            * <img src="https://i.loli.net/2020/11/19/VqyKxCv1nstEA8N.png" alt="图片" style="zoom:80%;" />
            * 简要参考[STL sort的comp函数注意事项](https://blog.csdn.net/weixin_43759874/article/details/90210520)-CSDN
            * 详细[参考一次stl sort调用导致的进程崩溃](http://blog.sina.com.cn/s/blog_532f6e8f01014c7y.html)-博客
            * <img src="https://i.loli.net/2020/11/19/3aiOFcNhmLszKu1.png" alt="图片" style="zoom:80%;" />
# [HZOJ-508](http://oj.haizeix.com/problem/508)：两人过河

![图片](https://i.loli.net/2020/11/19/4xUrle2dX7jokfH.png)

**样例输入**

```
4
1
5
2
10
```
**样例输出**
```
17
```
* 思路
    * 先排序
    * 四种情况
        * 1人
        * 2人
        * 3人：最快的人当工具人
        * 4人
            * ①传手电的速度保证最快
            * ②过桥效率最高（当次快很快、次慢很慢的时候）
            * 每次找**过两个人**的最快情况
            * <img src="https://i.loli.net/2020/11/19/1BljtwIMQxpKPUc.png" alt="图片" style="zoom: 33%;" />
* 代码
    * <img src="https://i.loli.net/2020/11/19/kc4StUpT8m6JjIn.png" alt="图片" style="zoom:67%;" />
    * 妙在2人2人地计算，两种方式取最小
# [HZOJ-509](http://oj.haizeix.com/problem/509)：智力大冲浪

![图片](https://i.loli.net/2020/11/19/9j4pmx5DdK3qhbE.png)

**样例输入**

```
10000
7
4 2 4 3 1 4 6
70 60 50 40 30 20 10
```
**样例输出**
```
9950
```
* 思路
    * 排序：扣钱数、时间←结构体
        * 先按扣钱数排序（多在前），再按时间排序（少在前）
    * 时段标记数组：记录某时段占用情况
        * 对于每个任务尽可能靠后去完成
* 代码
# [HZOJ-518](http://oj.haizeix.com/problem/518)：金币

![图片](https://i.loli.net/2020/11/19/AexawI8mYNnQr4p.png)

**样例输入**

```
10
```
**样例输出**
```
30
```
* 思路
    * 两层循环：给多少钱（1 ~ x）；天数（1 ~ i）
* 代码
# [HZOJ-513](http://oj.haizeix.com/problem/513)：楼层编号

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/VGNGSto.png)

**样例输入**

```
14 3
```
**样例输出**
```
12
```
* 思路
    * 枚举虚假楼层（1 ~ m），可以做为真实楼层编号存在则加一
    * 有非暴力解法
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/TY1jbl7.png" alt="图片" style="zoom:67%;" />
    * ans记得初始化~
    * if里只有++操作时，可以整合
# [HZOJ-514](http://oj.haizeix.com/problem/514)：火柴棒等式

![图片](https://i.loli.net/2020/11/19/ZOgEB9dPubQDr5s.png)

**样例输入**

```
14
```
**样例输出**
```
2
```
* 思路
    * 每个数对应的火柴数是固定的
    * 范围估计（最多24根火柴）
        * 111 + 111 左边最大的数？
        * 其实还可以和0、1搭配
        * 范围会更大：0 ~ 2000
        * 加数1、加数2可重复
    * 暴力枚举
        * 枚举两个加数，看和是否满足要求👈中转函数+苦力函数
* 代码
    * <img src="https://i.loli.net/2020/11/19/HmECdfJ5NwrDLa2.png" alt="图片" style="zoom:67%;" />
    * 枚举👉中转👉苦力
# [HZOJ-515](http://oj.haizeix.com/problem/515)：比例简化

![图片](https://i.loli.net/2020/11/19/gkn4zOIZGACvjK5.png)

**样例输入**

```
1498 902 10
```
**样例输出**
```
5 3
```
* 思路
    * 范围不大，L上限是100
    * 从小往大枚举
        * 两层循环
        * 不需要判断互质，如果存在，则前面肯定有满足条件的互质的答案
    * 枚举→判断→保留→给出最贴合答案的
* 代码
# [HZOJ-516](http://oj.haizeix.com/problem/516)：奶牛碑文

![图片](https://i.loli.net/2020/11/19/4fTsneSJNpCrYhx.png)

**样例输入**

```
6
COOWWW
```
**样例输出**
```
6
```
* 思路
    * 方法一：直接枚举，三层循环？❌过于暴力，O(N ^ 3)
    * 方法二：空间换时间
        * 对于每个O，能组成的COW数 = 前面C的数量 * 后面W的数量
        * 扫两遍数组，记录 O(N)
            * **前缀和**：到这个位置为止，有多少个C
            * **后缀和**：到这个位置为止，有多少个W
        * 再扫一遍数组，找O O(N)
            * **ans + 前C数 * 后W数**
        * 时间：O(N) 空间：O(N)
    * 引申：统计PUSH，时间复杂度O(N ^ 2)
        * 扫两遍，统计前缀和P，后缀和H
        * 找到U后，再找S，计数
        * 参考后面代码里的“同时找”技巧，应该也能省一个后缀和数组空间，找到S后找U
* 代码
    * <img src="https://i.loli.net/2020/11/19/yzKl4RpwLeVkIJr.png" alt="图片" style="zoom:67%;" />
    * 统计后缀和“同时找”O，节省了后缀和数组的空间0
    * int型相乘要注意溢出问题
# [HZOJ-517](http://oj.haizeix.com/problem/517)：三角形个数

![图片](https://i.loli.net/2020/11/19/fUHE9y5pbvRwn16.png)

**样例输入**

```
10
```
**样例输出**
```
2
```
* 思路
    * 关键在于**不能有重复**的三角形
        * 确定枚举方式，按大小边来枚举
        * 短边i：1 ~ n / 3
        * 中边j：i ~ (n - i) / 2
        * 长边：判断n - i - j < i + j，成立则ans++
    * 定了大小边，可以避免重复
* 代码
    * <img src="https://i.loli.net/2020/11/19/sxKrL2XFpa1tMck.png" alt="图片" style="zoom:67%;" />
    * 枚举最短边→次短边，保证不会重复
# [HZOJ-519](http://oj.haizeix.com/problem/519)：优雅数

![图片](https://i.loli.net/2020/11/19/ypYE1GoXMcLQlRN.png)

**样例输入**

```
110 133
```
**样例输出**
```
13
```
* 思路
    * 优雅数：只有一个数字不一样，其余都一样
    * 枚举题
    * ❌如果直接枚举，再判断是不是优雅数，量太大！10 ^ 16
    * **先枚举优雅数**YYXYYYYY，再判断是否在区间
        * 5层循环
        1. 枚举Y（一堆数）
        2. 枚举X（一个数）：X等于Y则continue
        3. 枚举数长：3 ~ 17
        4. X的位置：1 ~ 数长
            * X、Y如果有一个0，不能有前导0
            * X为0，X不能在第一位；Y为0，X必在第一位
        5. 构造优雅数，判断是否在区间内
* 代码
    * <img src="https://i.loli.net/2020/11/19/9StCD5q4NKbp3Ox.png" alt="图片" style="zoom:67%;" />
    * 换个角度枚举，很巧妙
    * 一个数可由数字组成、数字长度、数字位置决定
# [HZOJ-xxx](http://oj.haizeix.com/problem/513)：xxx



**样例输入**

```

```
**样例输出**
```

```
* 思路
* 代码

---


# 附加知识点

* 如果需要定义**超大数组**或者有**其他函数需用到**该数组，最好定义为全局变量，开辟堆空间
# 思考点

* string str;  // cin >> str 等价于 cin >> &str[0]
* ❓涉及到对象的，最好不要使用取址操作
    * 这个之后注意
# Tips

* 
---


# 课程速记

* HZOJ-506
    * <img src="https://i.loli.net/2020/11/19/Wk3NBHvIcAr1uPq.png" alt="图片" style="zoom: 50%;" />
    * 输出时1.0提升类型
