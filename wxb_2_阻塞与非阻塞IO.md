---
title: '2 阻塞与非阻塞IO'
date: 2021-01-05 18:00:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107762225-BooqUYFPnFUcfmkR.png" alt="图片" style="zoom:67%;" />
# 课程内容

理解两者的真正含义及区别，能回答以上问题

## 两者的字面意思

**上班**

* 阻塞：上班时堵车了，等或者使用其他方法，总之要去上班
* 非阻塞：上班堵车了，直接不去了

**老妈让买酱油**

* 阻塞：没有酱油了，问妈要不换别的，或者换别的买
* 非阻塞：没有酱油了，不买了

**让小明写报告**

* 阻塞：小明写的时候，等他写完
* 非阻塞：不用干等小明写，之后再告知结果 [自己问小明-同步 / 小明主动告诉我-异步]

**烧开水**

* 阻塞：烧开水的过程中，你不能干其他事情，只能站那等水开
* 非阻塞：烧开水的过程中，你可以干其他事情，比如去客厅看看电视
* 参考[同步异步、阻塞非阻塞极简解释](https://blog.csdn.net/evanxuhe/article/details/79627709)——CSDN
## 引入

* 回顾open函数中的O_NONBLOCK
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108770936-image-20210120101248947.png" alt="image-20210120101248947" style="zoom:67%;" />
    * 一切皆文件，所以一切都可以阻塞/非阻塞
* 分析写文件的过程
    * 打开→write→关闭
    * 其中，write属于系统调用，具体过程如下
        * 数据→内核→磁盘：内核拷贝数据，放在缓冲区 [块缓冲]，清刷缓冲时，调度IO设备，找到inode和block，将数据写入磁盘
            * 第一步数据→内核是用户态，第二步内核→磁盘是内核态，存在转换
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107769477-49W3JvqJBoPx518K.png" alt="图片" style="zoom:67%;" />
            * 参考[怎样去理解Linux用户态和内核态？](https://zhuanlan.zhihu.com/p/69554144)——知乎
        * NON_BLOCK设置的是用户态 [数据→内核 或者 内核→数据] 的过程
            * 大部分的阻塞是在从内核拿数据出来
        * 内核→磁盘的过程实际是阻塞的
            * 为了方便上层应用快速返回写的状态，内核将数据写到磁盘的过程，普通用户不需要感知
            * 内核拷贝完数据，程序返回，普通用户就认为写成功了，但数据很有可能还在缓冲区 [缓冲IO]
## fcntl

操作文件描述符 [可以将文件变成非阻塞的]

* man fcntl
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107800597-mHsHaP3QKLEGln0H.png)
    * fd：文件描述符。最常见的文件描述符：0、1、2
    * cmd：操作方式
    * ... /* arg */
        * 可变参数
        * arg，指示该参数是前面参数 [cmd] 的参数，其含义取决于cmd
* 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107831216-UVmoJDtruEWV1MtW.png)
    * cmd在圆括号里指示是否有可变参数，最多一个
    * 可变参数类型一般是int，使用用宏定义，本质是位掩码，通过位运算改变状态
    * 其中有一类cmd：与【文件状态标志】相关
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107831845-tFOQgVaCC0vMXZyq.png)
        * 即可获得或设置文件状态 [如O_NONBLOCK]
* 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107881105-PHRHu5uukqfvufk7.png)
    * 观察返回值，考虑程序中的判断
    * 出错都是返回-1，设置操作成功都是返回0
## select

同步I/O多路复用 [接口]

* man select
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107915089-EZPDEZqhQn7UQoIA.png)
    * nfds：文件描述符的数量
    * fd_set：文件描述符的集合
        * 可读、可写、异常
        * 底层是用数组是实现的
    * timeout：时间间隔
    * 四个宏用来操作集合，见后
* 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107956858-TNjfC34rpmnfF6zl.png)
    * 允许程序去监控多个文件描述符，等待一个或多个文件描述符的I/O操作"ready"的情况
        * ready：就绪，可以对文件进行相应的IO操作，如没有阻塞的读，或足够小的写
    * 可监控的文件描述符数量小于FD_SETSIZE [一般为1024]
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611107995883-XRr8EGUd4JUHkqVG.png)
    * 退出的时候，每个文件描述符集合会被修改，只留下**状态发生变化**的文件描述符，起指示作用
        * 所以，如果循环使用select，每次调用select前需要重新初始化每个集合
    * 集合可以是NULL，说明该类事件中没有文件被监控
    * **宏**用来操作集合
        * FD_ZERO：清空一个集合
        * FD_SET：向一个集合添加一个文件描述符
        * FD_CLR：从一个集合移除一个文件描述符
        * FD_ISSET：判断一个文件描述符的所属集合，可用于select返回后，因为集合已被修改
    * nfds的值是三个集合中**数字最大**的文件描述符**加一**
        * 文件描述符的索引是从0开始的
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108016609-wPCr2X2UBeo7kFqB.png)
    * struct timeval timeout
        * 指定select阻塞着等待每个文件描述符就绪的时间间隔
            * 这里阻塞的含义就是单纯的阻塞，不是阻塞I/O的阻塞
            * [个人理解] 同步I/O的多路复用的同步就体现在这
        * 三种停止阻塞的情况
            * 一个文件描述符就绪
            * 信号中断 [kill]
            * 超时
        * 时间间隔不是精确的，很难做到真正的精确
            * 系统时钟粒度、内核调度延迟
        * timeval结构体中有两个成员：秒、微秒
            * 如果两者都为0，就会立即返回，可用于轮询 [polling]
            * 如果为NULL，就会无期限等待
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108032835-V0rcgd7VPmPmaoX4.png)
        * timeout更新功能只在Linux上生效
        * 为了兼容性，尽量别用，尽量使用比较公共的功能
* 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108058130-M8x22xusJBwEWCXt.png)
    * 返回此时所有集合中文件描述符 [ready] 数量
        * 根据数量，再通过宏定义FD_ISSET询问**所有**文件描述符的所在集合，来感知状态变化
        * 0：时间过完了，也没有感兴趣的事件发生
        * -1：error，并设置errno；此时集合不会改变，timeout变成未定义的了

[PS]

* select → poll → epoll，越来越高级
    * man poll
    * man epoll
    * 均与select做的任务相似
# 代码演示

## 实现使文件非阻塞和阻塞的接口

* common.h
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108109613-pyIliiwINQWAx8j7.png" alt="图片" style="zoom:67%;" />
    * 两个接口
* head.h
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108116958-UCjXE6jYwB53GuqO.png" alt="图片" style="zoom:67%;" />
    * 头文件的顺序是有讲究的，一般把自己本项目的头文件放后面
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108156511-TUjvS8o0T7KS3EUa.png)
    * 参考[#include 的路径及顺序](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/headers/#include)——Google C++编程风格
* common.c
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108116041-KOwB903se7KWKZoz.png" alt="图片" style="zoom: 67%;" />
    * 设置flag时不能改变原有的flag
    * 👉先get，再通过位或 / 位与来添加 / 删除flag
## 非阻塞的0号文件

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108513035-SCfWf3bhrHrFcADU.png" alt="图片" style="zoom:67%;" />
* 常用文件0、1、2不用手动打开
    * 创建进程时自动打开这三个文件
    * 它们是继承过来的
* 编译命令：`gcc 1.test.c ../common/common.c  -I ../common/`
    * 编译记得添加common.c文件
* 0号文件设置成非阻塞后，关键变化在于0号文件的读写变成了非阻塞的
    * ① 没有sleep
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108225364-POGCJtmenGJaLd3z.png" alt="图片" style="zoom:67%;" />
        * scanf就是读0号文件
        * 但没有数据，直接走，不会阻塞着等0号文件有数据
    * ② 有sleep
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108187126-EnvG7AqfW0QYe0ur.png" alt="图片" style="zoom:67%;" />
        * 在sleep的5秒里，在终端输入数据 [+回车，行缓冲]
            * 进程暂停，不占用CPU
            * 但标准输入流仍然打开着，用户在终端输入的数据会由内核传给0号文件
                * 文件是内核维护的，sleep的过程内核可以给文件写数据
        * 在sleep结束后，scanf读走0号文件的数据
    * 【明确】非阻塞是指某个对象，比如0号文件；而不是某个函数、某个操作
* [PS]
    * 阻塞：可能浪费时间
    * 非阻塞：可能浪费资源
        * 一般用于大型的程序、高并发服务器
## 对0号文件的select

* 拷贝自man手册——man select里的EXAMPLE
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108317862-UsHS4m7Gy8fKUanU.png" alt="图片" style="zoom:67%;" />
* select可以感知到I/O的到来
* 运行效果
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611108847552-image-20210120101405659.png" alt="image-20210120101405659" style="zoom:67%;" />
    * 终端输入ls，程序结束后还会执行ls
    * 因为缓冲区的内容没有被取走 [如scanf]，最终被zsh接走了
* select在这监控的时间间隔里发生了阻塞
    * 在所有的用户层面的程序里面，所谓的阻塞就是睡觉 [sleep()]
    * 阻塞终止条件：ready、signal中断、超时
* [延伸应用]
    * 给阻塞的地方设置一个定时，超时则使用默认值
        * 避免异常情况阻塞过久 [SSH主机不可达...]
        * 更人性化
# 附加知识点

* I/O感知的方式可以有很多种，其中内核完全知道IO的到来
# 思考点

* scanf的时候，在终端输入了回车，是将数据放入了缓冲区，还是清空缓冲区给了scanf呢？
    * [一文带你读懂C/C++语言输入输出流与缓存区](https://www.eet-china.com/mp/a19757.html)
    * 是前者
# Tips

* 课程预告：多进程 [fork...]
* 可以考虑cp的实现
    * 考察文件的读写操作
    * 必须阻塞

---


