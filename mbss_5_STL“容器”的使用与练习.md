---
title: '5 STL“容器”的使用与练习'
date: 2020-11-11 18:12:00
tags: [计算机,编程算法,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
"容器" 为什么要加引号呢~请往下看

# 课程内容

>模板<类型> 名;  // 自定义类型也可以，如struct
## 队列与栈

### queue

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/d3PrnN0.png" alt="图片" style="zoom: 33%;" />

* queue<int> que;
* 名.方法()
    * que.push(5);  // 入队
    * que.pop();  // 出队
    * que.front();  // 队首元素
    * que.size();  // 元素个数
    * que.empty();  // 是否为空
### stack

* stack<int> sta;
* 名.方法()
    * sta.push(5);
    * sta.pop();
    * sta.**top()**;       // 栈顶元素，唯独与queue不一样的
    * sta.size();
    * sta.empty();
### 本质

* 它们的底层是由**deque**实现的
    * double-ended queue，双端队列
    * 参考[deque](https://zh.cppreference.com/w/cpp/container/deque)-cppreference
* 它们是对deque的封装，实际是适配器
    * deque才是正儿八经的容器
## 向量与优先队列

### vector

【插：在C语言中，不能定义变长数组，❌不可这样定义：int n; scanf("%d", &n); int num\[n\]】

* vector<int> v;
* 名.方法()
    * v.push_back(5);  // 在尾部添加一个元素**O(1)**
    * v.size();               // 元素数量
    * v.insert(1, 6);      // 插入元素**O(N)**，用push_back更高效
* vector<vector<int> > v2;  // 动态二维数组
    * C++11标准之前"> >"之间要有空格，否则易被误认为是**右移**操作
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/avE4bqJ.png" alt="图片" style="zoom: 50%;" />
* 初始化，参考[vector的几种初始化及赋值方式](https://blog.csdn.net/yjunyu/article/details/77728410)-CSDN
* cin到vector需要先将vector初始化出空间大小
### priority_queue

* priority_queue <int> que;
    * int 后可以添加排序的对象
    * 参考[C++ priority_queue(STL priority_queue)用法详解](http://c.biancheng.net/view/480.html)-C语言中文网
* 名.方法()
    * que.push(5);
    * que.pop();
    * que.**top()**;                     // 堆顶元素，默认是**大数**在上
    * que.size();
    * que.empty();
* 自定义结构型优先队列需要重载**小于**号
    * bool operator <(const struct名 &变量) {}
    * 注：重载小于号，对于这个容器，在里面实现的若是
        * a) <，则从大到小排列，对应最大堆
        * b) >，则从小到大排列，对应最小堆
        * 有点绕，详细见代码演示-priority_queue
    * 注：所有需要排序的容器结构，都是重载小于号
### 本质

* vector底层是用数组实现的
* priority_queue底层是用堆实现的
## 字符串 string

类

* string str;
* 名.方法()
    * str.size()                    // 等同于 str.length()
    * str.find(s2, pos = 0);  // 在str中查找s2，默认从头(pos = 0)开始查找，找到返回下标
        * 底层方法实现取决于编译器
        * 判断是否找到（no position）
```c++
if (str,find(s2) != string::npos) {
  // static const size_t npos = -1;
  // 找到了
} else {
  // 未找到
}
```
*   * str.insert(x, s2);                  // 在下标x处插入s2
    * str.substr(2);                       // 从下标2截取到最后
    * str.substr(2, len);                // 注意第二个参数是长度
    * str.replace(index, len, s2);  // 同样注意第二个参数是长度
* 重载了很多运算符
    * \+
        * string a = string("123") + string("a");
        * += 慎用
            * a += string("123") + string("456");  // "+="优先级仅高于","
            * ❓ 想象一下定义了多少临时变量，值拷贝了多少次
    * ==、>=、<=、>、<
        * 根据字典序判断
    * =
        * str = "123a"
        * 可以直接赋值，不像C语言需要使用strcpy
## 键值对 map

映射

* map<string, int> m;  // "123" → 456
* 方法
    * []
        * 赋值、访问
        * 如果访问的key不存在，就会自动创建一个，按类型的默认值赋值
        * 有隐式开销，因为底层是红黑树，查找效率为O(logN)
    * insert、find、erase
    * count
        * 返回某个key多少个
        * 但map里没有重复的，所以只返回0、1
        * 可用来判断key是否存在
* 底层实现是红黑树【RBTree】
    * 有序的结构，map默认升序排
    * 如果key对应的是自定义结构体，需重载<号
* 扩展
    * multimap
        * 可以有多个重复的键存在
    * unordered_map   ❗c++11里出现
        * **无序**
        * 底层实现是哈希表，查找、插入效率均为O(1)
        * 自定义结构需要手动实现哈希函数
            * 参考[C++ STL无序容器自定义哈希函数和比较规则](http://c.biancheng.net/view/7254.html)-C语言中文网
## 集合 set

* 可用来去重、排序
* 底层和map一样，红黑树，必要时也需重载“<”
* 扩展
    * multiset
    * unordered_set    ❗c++11
## 对 pair

* 对儿
* pair<int, int>
    * pair.first       前一元素
    * pair.second 后一元素
* [make_pair](http://www.cplusplus.com/reference/utility/make_pair/)可快速创建一个临时的pair
# 代码演示

## queue

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/WNbZFir.png" alt="图片" style="zoom:67%;" />

* 两种数据类型的队列
* que.empty()和que.size()都是O(1)的，底层有一个数据域存储
* 默认生成构造函数

输出

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/pG46OnR.png" alt="图片" style="zoom:67%;" />

## stack

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/pohezwA.png" alt="图片" style="zoom:67%;" />

* 与queue的区别：名字、头文件、top()方法

输出

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/zwD7mXF.png" alt="图片" style="zoom:80%;" />

## vector

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/SC1y6BV.png" alt="图片" style="zoom: 80%;" />

* 比数组更高级，在leetcode里常用
* 3种插入方式

输出

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/v3KLlzN.png" alt="图片" style="zoom:80%;" />

## priority_queue

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Yd7fDX3.png" alt="图片" style="zoom:80%;" />

* 自定义结构型队列要记得重载小于号
* ❗重载的是小于号，但完成的是从大到小输出！

输出

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/c0XJHY0.png" alt="图片" style="zoom:80%;" />

* 第二部分里，实现了从大到小排列，但重载的是小于号，实现的也是小于号
## map

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/WGOLe3p.png" alt="图片" style="zoom:67%;" />

* 对于自定义结构，map需重载<，unordered_map需定义hash函数
* 对于不存在的key，访问时会自动创建，并赋值类型的默认值

输出

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/O8zLrVl.png" alt="图片" style="zoom:80%;" />


---


# 练习题

# [HZOJ-383](http://oj.haizeix.com/problem/383)：周末舞会

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/8LDd5M8.png)

**样例输入**

```
3 5 6
```
**样例输出**
```
1 1
2 2
3 3
1 4
2 5
3 1
```
* 思路
    * 队列
    * 出队、放队尾
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Z77YFKG.png" alt="图片" style="zoom:80%;" />
    * 队列的基本操作
# [HZOJ-378](http://oj.haizeix.com/problem/378)：字符串括号匹配2

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/wWkq8UK.png)

**样例输入**

```
a(cc[])bbb()@
```
**样例输出**
```
YES
```
* 思路
    * 字符栈
    * 匹配：YES，如( [ ] )
    * 不匹配：NO
        * 括号错开了，如( [ ) ]
        * 碰见右括号时，栈为空，如( ) )
        * 字符串结束了，栈还不为空，如( ( { } )
    * **注意**：不能使用三个变量单纯记录个数，因为括号有顺序问题，比如([)]
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/fQ5ePaK.png" alt="图片" style="zoom:80%;" />
    * 注意三种不匹配情况
    * 匹配时将左括号出栈
# [HZOJ-376](http://oj.haizeix.com/problem/376)：机器翻译

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/HVsMuHJ.png)

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/v4hEv8Z.png)

**样例输入**

```
3 7
1 2 1 5 4 4 1
```
**样例输出**
```
5
```
* 思路
    * 队列
    * 用**标记数组**记录单词
        * mark[x] = 1、0
        * 数据不大（文章长度不超过1000），没必要用map、set之类的
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/zhe09jQ.png" alt="图片" style="zoom:67%;" />
    * 关键在存单词的队列和标记单词的数组
    * 注意内存已满的情况
# [HZOJ-379](http://oj.haizeix.com/problem/379)：仓库日志

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/rr9TCcK.png)

**样例输入**

```
13 0 1 0 2 2 0 4 0 2 2 1 2 1 1 2 1 2
```
**样例输出**
```
2
4
4
1
0
```
* 思路
    * 0、1、2：入库、出库、查询
    * 先进后出：需要一个货物栈
    * 查询功能（输出最大值）：还需要一个记录最大值的栈**【关键】**
    * 注意：每次的货物均不同，所以题目被简单化了
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/xgRa1rL.png" alt="图片" style="zoom:67%;" />
    * 共两个栈！
        * 对于本题，货物栈可以不用，只是助于理解
        * 因为本题不需要管出栈的货物是什么，而最大栈每次入库都记录最大值，会重复记！
# [HZOJ-382](http://oj.haizeix.com/problem/382)：报数

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/EmufuFC.png)

**样例输入**

```
7 3
```
**样例输出**
```
4
```
* 思路
    * 约瑟夫环问题
    * 【精髓】队列：安全的人扔队尾，不安全的人弹出去
    * 结束条件：当队列的size为1时
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/GbofGQU.png" alt="图片" style="zoom:80%;" />
    * push的 i 是编号
    * pop没有返回值吗？
        * 这里提到的容器里，pop、push都没有返回值
        * 详见[pop](https://zh.cppreference.com/mwiki/index.php?title=Special%3A%E6%90%9C%E7%B4%A2&search=pop&button=)-cppreference
# [HZOJ-384](http://oj.haizeix.com/problem/384)：敲七

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/pzJkGvU.png)

**样例输入**

```
4 3 6
```
**样例输出**
```
3
```
* 思路
    * 与上题如出一辙，注意细节
    * 区别在于
        * 输入：第x个人从t开始报
        * 判断条件：含7或者7的倍数
        * 报数不重置，一直+1
* 代码
# [HZOJ-385](http://oj.haizeix.com/problem/385)：海港

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/EIkTw4f.png)

**样例输入**

```
4
1 4 1 2 2 3
3 2 2 3
86401 2 3 4
86402 1 5
```
**样例输出**
```
3
3
3
4
```
* 思路
    * 输入：到达时间、人数、每个人的国籍
    * 队列 +【标记数组/map】+ 国家数量的变量
        * 队列存船或人都可以
            * 因为船里的人不固定，存人更方便
        * 先根据时间减人，再加人
        * 国家数量：如果新来一个，+1；如果走了最后一个，-1
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/39WlM6x.png" alt="图片" style="zoom:67%;" />
    * 关键在用到的队列、标记数组和统计国家数量的变量
    * 有点像滑动窗口？略像，但一般滑动窗口，其数据是早就固定好的
    * 直接复制数据输入，输出格式有点奇怪？应该是换行符的问题
    * 33行快速定义结构体：(struct名){..., ...}
        * 不加圆括号()也没问题，但是加括号可能更清晰
# [HZOJ-569](http://oj.haizeix.com/problem/569)：溶液模拟器

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/ys8WUyb.png)

**样例输入**

```
100 100
2
P 100 0
Z
```
**样例输出**
```
200 50.00000
100 100.00000
```
* 思路
    * 撤销操作：用栈！存每一次操作后的质量和浓度 [或者] 盐的质量和总质量
    * 化学知识回顾
        * 溶液组成：盐 + 水
        * 浓度：盐 / (盐 + 水)
        * 质量：盐 + 水
* 代码
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/nUpS7eL.png" alt="图片" style="zoom:67%;" />
    * 存盐的质量和总质量便于理解计算
        * 都存为double类型，输出时注意转换
    * 撤销操作：先减再弹
# [LC-](https://leetcode-cn.com/problems/implement-queue-using-stacks/)[232](https://leetcode-cn.com/problems/implement-queue-using-stacks/)：用栈实现队列

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/bWnmrnS.png)

* 思路
    * 两个栈实现队列
    * 心中有这样一个过程👇
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/DEf8com.png" alt="图片" style="zoom:80%;" />
* 代码
```c++
class MyQueue {
public:
    // 在此处声明栈，否则不好调用
    stack<int> s1, s2;
    /** Initialize your data structure here. */
    MyQueue() {
        // 不用管
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        // 先把s1栈的元素全倒到s2栈
        while (!s1.empty()) {
            // 先push再pop，顺序不能反
            s2.push(s1.top());
            s1.pop();
        }
        int t = s2.top();  // 此时取出要pop的值
        s2.pop();          // 出栈
        // 倒回来
        while (!s2.empty()) {
            s1.push(s2.top());
            s2.pop();
        }
        return t;
    }
    
    /** Get the front element. */
    int peek() {
        // 折腾到s2
        while (!s1.empty()) {
            s2.push(s1.top());
            s1.pop();
        }
        int t = s2.top();  // 临时变量t存值，还得放回来再return
        // 折腾回来
        while (!s2.empty()) {
            s1.push(s2.top());
            s2.pop();
        }
        return t;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return s1.empty();  // 直接判断s1即可
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```
*   * 每次操作完，值一直是存放在s1里
    * 栈的声明位置：放在类的全局里，才能被其他函数访问
        * 放在初始化函数MyQueue()里是否可以用this调用到？
        * ❌不行，this指的是整个大类，之后学习类的时候注意
    * 【进阶】你能否实现每个操作均摊时间复杂度为 O(1) 的队列？换句话说，执行 n 个操作的总时间复杂度为 O(n) ，即使其中一个操作可能花费较长时间。
        * **关键**在于：pop把元素从s1倒腾到s2的时候，不倒腾回来了
        * 这样s2的顺序就是队列出队的顺序
        * 每次pop只有s2为空时，才把s1的元素倒腾过来，否则直接pop s2即可
        * 参考[用栈实现队列-官方解答](https://leetcode-cn.com/problems/implement-queue-using-stacks/solution/yong-zhan-shi-xian-dui-lie-by-leetcode/)-方法二
# [LC-](https://leetcode-cn.com/problems/implement-stack-using-queues/)[225](https://leetcode-cn.com/problems/implement-stack-using-queues/)：用队列实现栈

![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/vIWXqqi.png)

* 思路
    * 其实只需要一个队列
        * 转移元素时，放队尾即可
            * ①可以在push的时候调整顺序
            * ②也可以在pop的时候操作
        * 通过queue.size()控制转移的边界
* 代码
```c++
class MyStack {
public:
    queue<int> que;
    /** Initialize your data structure here. */
    MyStack() {
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        que.push(x);
        // 将前面的所有元素依次放到后面
        for (int i = 1; i < que.size(); i++) {
            que.push(que.front());
            que.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int t = que.front();
        que.pop();
        return t;
    }
    
    /** Get the top element. */
    int top() {
        return que.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return que.empty();
    }
};
/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```
*   * 把复杂的操作给push后，其他操作像栈一样使用
# 附加知识点

* 快速定义结构体：(struct名){..., ...}
# 思考点

# Tips

---


# 课程速记

* 
