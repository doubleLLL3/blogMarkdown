---
title: '6 多线程编程基础'
date: 2021-01-21 18:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

>进程间通信复杂 [内存空间独立]、切换成本高 [时间局部性]，所以发明了**线程**
## 线程

一个进程的分支 [pthread]，本质上是一个轻量级的进程

* 通信方便，因为一个进程里的多个线程共享内存
* 切换成本低，因为内存是共享的，在切换线程时不需要置换缓存
## pthread_creat

创建一个新的线程

* man pthread_create
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/jH6l4ktXs9jdX0ni.png)
    * thread：线程id [注意：不是数字类型，不能直接用==判断，见pthread_equal]
    * attr：属性
    * arg是start_routine的参数
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/MDmcuuDnijAqecYG.png)
    * 开启一个新的线程，并执行start_route函数
        * start_route函数只能收一个arg参数 [多个参数可用结构体封装]
    * 线程有**4种**终结的方式 [作为工具人，死亡方式很重要]
        * ① 自杀：自己调用pthread_exit
            * 同一进程下的线程可使用pthread_join接收它的死亡状态 [有点像wait]
        * ② 正常死亡：从start_routine函数中返回
            * 与pthread_exit方式等价
        * ③ 他杀：pthread_cancel
        * ④ 同归于尽：进程中的一个线程调用exit，或者主线程从main函数中返回
            * [PS] 如果一个线程导致内存崩溃，极有可能也产生同归于尽的效果，即进程中的所有线程都死亡
    * attr可以为NULL，对应默认属性
    * 调用成功后，会将线程id保存在thread变量中，后续都可以通过这个id使用它 [类似文件描述符]
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/pbyVydKn9MfsjXhY.png)
    * 0，成功；否则，失败
## ——3种终止线程的方法——

## pthread_exit

线程自杀

* man pthread_exit
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/WnQTGBzOXYVvSZv8.png)
* ❗ 线程自杀，将retval传给join的线程 [线程默认是可加入的]
* 执行完pthread_cleanup_push注册的函数，随后释放线程特有的数据
    * 进程中共享的资源不会被释放 [因为还有兄弟线程]
    * atexit注册的函数不会被调用 [这是属于进程的]
* 在最后一个线程结束后，进程以exit(0)的方式结束，释放进程共享的资源，并执行atexit注册的函数
* 【注意】线程和进程的关系
## pthread_cancel

给一个线程发送取消请求 [他杀]

* man pthread_cancel
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/np54JOnreoVMXg1q.png)
* 线程取消的可能性和时间取决于两个属性：state、type
* state
    * 可杀 [默认]
    * 不可杀：此时收到的取消命令会排在队列中
* type
    * 推迟的 [默认]：等到线程的下一次调用
    * 异步的：立即，但系统不能保证
## exit [进程相关]

终止普通进程

* man exit
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/AVwIUox6if8SLVby.png)
* 传给父进程的值为：status & 0377
    * 注意：0377是8进制，对应二进制的8个1，也就是只保留status的低8位
* 使用atexit和on_exit注册的函数会以与注册相反的顺序被调用
    * 可以套娃：注册的函数中还可以有注册，并且会放在调用列表的最前面
    * 如果注册的函数没有返回，比如调用了_exit或者使用信号自杀，剩余的函数不会再被调用，exit相关的处理也会被禁止
    * 多次注册函数，会被多次调用
* ⭐ exit后会刷新并关闭所有标准IO流
## ——监控线程状态相关——

## pthread_join

等待线程终止

* man pthread_join
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/077o0sUm3RZ6R9Wg.png)
* 可类比进程中的wait函数
* retval接收线程退出状态
    * 如果线程是自杀的，则复制pthread_exit中的retval值
    * 如果线程是他杀的，则赋值为PTHREAD_CANCELED
* [思考] 这里的retval是二级指针，也就是指针的指针，为什么？
    * 表面原因：pthread_exit里的retval是一个指针，按照惯例，这里就要用二级指针了 [同理，如果接收的是一个int数据，这里就用int *]
    * 进一步原因：为了可以修改传过来的指针
    * 这里有一个博客也提到了：[探讨pthread_join()函数第二个形参为啥是二级指针问题](https://blog.csdn.net/yunjie167/article/details/79181333)——CSDN
## pthread_detach

分离线程

* man pthread_detach
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/PtGHoPiQNuXaZq85.png)
* 线程被分离后，其终止时系统会自动回收其资源，而不需要其他线程阻塞等待它终止了
* 一般与**pthread_self**配合使自己分离
    * pthread_self：获得调用线程 [自己] 的id
* 参考[pthread_join()与pthread_detach()详解](https://blog.csdn.net/weibo1230123/article/details/81410241)——CSDN
## ——附加——

## pthread_yield

让出处理器 [processor]

* man pthread_yield
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/V1thXYuXGSycRDa6.png)
* [类似sleep的效果]
* 该方法只用于某些系统，更标准的用法：sched_yield
    * 对于**协同式**系统，调用此函数来主动让出CPU
    * 对于**抢占式**系统，内核会进行调度，该函数没有太大意义，也可直接使用sleep
    * 协同式与抢占式，详见4 高级进程管理——[调度器分类](https://doublelll3.ml/wxb_4_%E9%AB%98%E7%BA%A7%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86/#%E8%B0%83%E5%BA%A6%E5%99%A8%E5%88%86%E7%B1%BB)
## pthread_equal

对比两个线程的id [不能直接用==判等]

* man pthread_equal
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/0gfchdbRd5xnHedT.png)
* 如果相等，返回一个非零值
## 线程池

>让一堆线程在池子里待命，随时工作

基本组成部分👇

①**任务队列**：存放需要处理的任务

* [循环队列更佳]
* 基本操作：init、push、pop

② 多个线程：时刻准备着，减少创建响应和销毁的时间

③ 线程功能：do_work()

* while(1)：等待任务加入
    * 1) 任务队列pop：弹出任务，供线程执行
    * 2) do-work()：在**CPU**中执行任务

❗ 注意：push和pop的时候都需要**加锁**，防止数据竞争 [饥渴的线程]

* 详见代码演示
## 内核线程、用户线程

>线程是谁产生的？线程模型
* 两者的区别主要在调度上：内核线程由内核调度；用户线程由用户进程调度
* 内核线程的优势
    * ① 每个内核线程有自己的时间片。所以其进程因为有多个线程，会拥有更多的处理器时间；而用户进程不会因为多分出了几个用户线程获得更多的处理器时间
    * ② 如果一个内核线程被阻塞，进程中剩余的线程还可以继续运行。如果一个用户线程被阻塞，整个进程就都会被阻塞
        * PS：如果一个内核线程给自己的进程发送sleep信号，这个线程依然可以继续运行
* 用户线程的优势
    * ① 切换成本低。不会涉及用户态向内核态的转换
    * ② 调度算法完全由进程控制。用户进程可以用自己的调度算法，所以自主性更好；而内核线程的调度，对用户来说是一个黑箱
* 所以可以结合两者的优势，设计既有内核线程，也有用户线程的**混合式**线程
# 代码演示

## 简单使用多线程

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/sooMBOL5SipQK6aJ.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/DBEawFUgrTtCjAip.png)
* 关注：pthread_create函数的使用
* 如果在创建线程后，没有usleep或者usleep的时间过短，可能发生：
    * 主线程return，从而使所有子线程同归于尽
    * 此时会发生某些输出出现两次的情况，如①、② [③为正常输出]
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/abWrAKlD57DgKlVO.png" alt="图片" style="zoom:67%;" />
    * 猜测：输出**缓冲区**的问题，在线程突然结束时，又输出了一次缓冲区的内容 [缓冲区没来得及更新]
    * [PS] 使用fflush也不能解决，可能因为线程是突然结束的
* 注意：所有线程都可以操作同一个地址的值
## 线程池

thread_pool.h

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/6XptmHbDsf4sL5Qq.png" alt="图片" style="zoom:67%;" />
* 任务队列的定义，及基本操作

thread_pool.c

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/NQZVy3QhDHwnV9Gf.png" alt="图片" style="zoom:67%;" />
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/C7SFUrIDWdVE53iN.png" alt="图片" style="zoom:67%;" />
* 注意：加解锁、发信号；判断队列满/空；判断指针是否到达队尾

1.test.c

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/AmS7s7oD4dPiyzBc.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/871FqVnrlsDWCzGf.png)
* 存储数据的buff为二维数组，可避免线程读取数据时，主线程通过地址改变了数据
* usleep的妙用：避免while(1)循环太耗CPU
* pthread_detach一般与pthread_self配合使用
* fgets：读取文件到缓冲区
    * man fgets
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/BwDjjbjdlYKqhsod.png" alt="图片" style="zoom:50%;" />
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/vjZr0627oSXkq0Xo.png)
    * 按行读
* 输出效果
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Wpek76ZtCITIvi0D.png)
    * 基本输出：push [任务队列输出]；pop [任务队列输出] + do_work [线程输出]
# 附加知识点

* 编译包含线程相关函数的文件时，注意使用-lpthread
# Tips

* 创建的一个进程，又代表一个主线程
* 多线程在单cpu中还是顺序执行的，所以在单核CPU上，不推荐使用多线程
    * 参考[对于多线程程序，单核cpu与多核cpu是怎么工作的](http://cnblogs.com/caihuafeng/p/5438753.html)——cnblogs

---


