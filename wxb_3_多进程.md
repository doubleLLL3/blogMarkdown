---
title: '3 多进程'
date: 2021-01-07 18:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 什么是进程

* 进程是程序在内存中的镜像，是正在运行的程序，是程序的实例化，是一个复杂的集合体
    * 包含开辟的内存空间、用户信息、组信息、权限、占用的资源、正在跑的代码、打开的文件等等
* 与之对应
    * ① 什么是程序
        * 程序是编译好的可执行的二进制文件，放在磁盘上
            * 就是一个普通文件，有x权限
        * 程序的集合是应用
    * ② 什么是线程
        * 线程代表一系列有顺序的、需要CPU执行的指令
        * 一个进程可能由一个或多个线程组成，同时执行指令
    * [PS] 进程是CPU**资源分配**的基本单位，线程是CPU**调度**的基本单位
## fork

创建一个子进程 [进程接口]

* man fork
* 原型 + 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611109489362-ZfxrF8PScEKwUiqI.png)
    * 返回值 [类型：pid_t]：进程id
    * 通过复制调用fork的进程 [父进程] 创建一个新进程 [子进程]，父进程和子进程运行在**相互独立**的内存空间
        * fork完成时，两者有一样的内容，之后内存的写、文件映射不会互相影响
        * 当内存发生变化时，才会发生真正的拷贝 [**写拷贝**的概念]
            * 否则共用的是同一份内存空间
    * ![image-20210120105027238](https://gitee.com/doubleL3/blog-imgs/raw/master/img/image-20210120105027238.png)
    * 父子主要有以下的不同点：
        * 孩子有自己唯一的PID，并且不和任何已存在的PID相同
        * 孩子认为的父亲PID [getppid] 与真实的父亲PID相同
        * 孩子不会继承父亲的内存锁
        * 孩子的资源使用量和CPU使用时间都会重置为0
        * 孩子不会继承待执行的信号、信号量、记录锁、计时器、异步IO操作
* 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/vbylHTxiazhBbUnQ.png)
    * 成功：在父进程中返回孩子的PID，子进程中返回0
        * 父亲无法再通过其他方式获得该孩子的PID，儿子可通过getppid获得父亲的PID
    * 失败：返回-1，并设置errno [没有创建子进程]
## wait

等待进程状态的改变

* man wait
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611110763371-2Zz745vSWoQa1HhS.png)
    * wstatus [int *]：返回子进程的状态
        * 如子进程中return、exit的值
        * 需要使用宏来解析，如WIFEXITED(wstatus)，详见代码演示—wait—二
* 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/LQNkTWGZlxH8DW2z.png)
    * 等待对象：调用进程的孩子
    * 状态改变情况：孩子被终止、被信号中断、被信号唤醒
    * 当有被终止的孩子时，
        * wait命令可以使**系统**释放孩子相关的资源
        * 否则 [不执行wait命令]，被终止的子进程就会变成**僵尸进程**[👇]
            * 死了的孩子没有被父进程察觉，其资源没有被释放
            * 可使用top查看
            * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/eHYmUjRqwO4dZTO6.png)
            * zombie即僵尸进程
    * 只要**一个**孩子已经改变状态，wait命令就会被立马返回
        * 否则，会阻塞直到有孩子改变状态或者信号中断
* 返回值
    * ![](https://gitee.com/doubleL3/blog-imgs/raw/master/img/image-20210120105331457.png)
    * 返回被终止的孩子PID或者-1 [出错，并设置errno]
## exec族

执行一个文件 [一切皆文件]

* man exec
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113616.png)
    * 有很多兄弟
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113629.png)
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113640.png)
    * 将用一个全新的进程镜像【替换】当前的进程镜像
        * [让孩子有一个全新的世界]
    * 第一个参数都是要执行文件的名字
        * path：完整路径
        * file：可以是PATH环境变量里的命令或完整路径
    * 整个族可概括为："exec + l/v + p/e/pe"
        * 参数名arg，表示是前面参数path的参数
        * l-list，所有参数放入一整个字符串中 [参数的传递方式]
            * 按惯例，arg0应与要执行文件的名字有关联
            * 必须以(char *) NULL结尾
        * v-vector，所有参数放入一个字符串**数组**中 [参数的传递方式]
            * 必须以null指针结尾
        * p-path，可执行文件的查找范围包括PATH环境变量
            * 复制了Shell查找命令的过程
        * e-env，允许指定环境变量
            * 变量-数值对
* 返回值
    * ![img](https://gitee.com/doubleL3/blog-imgs/raw/999fbebf7a51beade71042d90c3f2767d815682e/img/image-20210120105207913.png)
    * 只在错误发生时返回-1
## flock

在打开的文件上操作建议锁

[本质上是为了保护数据]

* man 2 flock
* 原型 + 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113307.png)
    * 通过文件描述符fd操作
    * 主要三种操作
        * LOCK_SH：共享锁
        * LOCK_EX：互斥锁
            * 互斥锁：如果有一个人访问，其他人就不能访问了
            * 举例：很多人上一个卫生间
        * LOCK_UN：解锁
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113710.png)
    * 0，成功；-1，失败
# 代码演示

## fork

**一、复制缓冲区、行缓冲**

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611110767830-Lfva3KqP6K3QJvSQ.png" alt="图片" style="zoom:67%;" />
* 输出结果
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113731.png" alt="图片" style="zoom:67%;" />
    * ❗ fork后面已经没有输出函数了，为什么输入suyelu，会输出**两个**suyelu？
    * 【事实】虽然fork后代码复制了一份给子进程，但是子进程只会执行fork后的代码
    * 【关键】缓冲区被复制了，里面还存有suyelu
        * printf中没有换行符，而标准I/O是**行缓冲**I/O，第13行执行完并不会刷新缓冲区
        * 当程序结束时，才**触发**刷新缓冲区的条件
    * [PS] zsh下可能只会输出一次suyelu，可能是zsh的优化？bash下有俩

**二、父子进程相互独立**

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113750.png" alt="图片" style="zoom:67%;" />
* 输出结果
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113804.png" alt="图片" style="zoom:67%;" />
    * ❗ 父进程一定先执行吗？
        * 不一定，父子进程完全独立，不相干，本质上谁先执行是由**内核**调度决定
        * 但父进程**极大概率**先执行，因为内核调度的每个进程有一个运行时间，父进程生了孩子后，它的运行时长还没到
* [PS]
    * 1号进程 <pid为1的进程> 是init进程，其它进程都是由它生出来的
    * 与人类世界相反，计算机世界的第一个进程一直活着，等着给子进程收尸

**三、创建10个子进程，并打印自己的序号**

10个子进程是亲兄弟

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/R0avb9MtFgxvki3w.png" alt="图片" style="zoom:67%;" />
* 如果不加18行的break
    * 将产生2^10个进程：1 -> 2 -> 4 -> 8 -> 16 -> 32 -> ... -> 2^10
    * 统计运行的父、子进程数量：ps -ef | grep -v grep | grep Ten | wc -l
        * [Ten为可执行程序名]
* sleep时长不会叠加
    * 进程遇到sleep，系统调度换到其它进程运行，最后等待时间只体现约10s
* i变量被子进程带走后就独立了，不会因父进程中i变量的改变而改变
## wait

**一、制造僵尸进程**

* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1611110771329-CMus7y0h82QWdIuL.png)
* 不用wait感知子进程的终止，即会产生僵尸进程
* 用户有多种查看僵尸进程的方式 [让程序在后台运行：./a,out &]
    * 基于ps，查看有defunct或Z标记的进程
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113854.png)
    * 基于top
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/6u1im6EIOIavYZJj.png)
    * 利用pstree可以看到僵尸进程的血缘关系
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/yhCLhbDHfQZqmYIv.png)
* [PS] 杀僵尸进程需要杀其父进程；该父进程的父进程是zsh，程序结束后，zsh会告知系统对父子进程收尸

**二、感知子进程返回状态**

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/MLjVDiVVQdKdp69I.png" alt="图片" style="zoom:67%;" />
* 程序在运行约2s后，输出如下：
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113921.png" alt="图片" style="zoom:67%;" />
* ❗ 为什么子进程return 1，父进程wait得到的status是256
    * 16位int型变量值为256 👉 其二进制对应第8位为1、其余位均为0
    * 再参考下图 [[Linux-UNIX系统编程手册 （上册）—26.1.3节](https://github.com/stelectronic/doc/blob/master/Linux-UNIX%E7%B3%BB%E7%BB%9F%E7%BC%96%E7%A8%8B%E6%89%8B%E5%86%8C%EF%BC%88%E4%B8%8A%E5%86%8C%EF%BC%89.pdf)]，问题有了答案
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/PGxHTodLXondHE1g.png?fileGuid=pmkxQbE7mNtX7RAN" alt="图片" style="zoom:67%;" />
    * 其实，在man手册中提到了可使用宏来检查状态
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120113944.png)
    * WEXITSTATUS(wstatus)则可以解析退出状态
    * 在源码中，每个宏对应了下面的位操作
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/wRGStsYZOHMKeoEf.png" alt="图片" style="zoom: 50%;" />
    * 所以在printf状态时，根据需求，通过宏处理以下即可
## exec族

【替换为全新的进程】

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114001.png" alt="图片" style="zoom:67%;" />
* 子进程在第17行被替换为全新的进程 [vim]，之后的代码永远不会被执行
    * fork后直接exec：不会在fork时复制父进程的内存空间又在exec时马上启用 [写拷贝概念：当内存发生变化时，才会发生真正的拷贝]
* wait(NULL)负责收尸
* execlp的第二个参数可以任意，但和第一个参数有关联更有意义
    * 在下面可以体现该参数的某方面意义
    * 如果将exec代码换成第17行，第二个参数随意取名
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Rc2ZjFfYdbIiVbU5.png" alt="图片" style="zoom:67%;" />
    * 生成可执行文件Test的源文件test.c如下：
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114023.png" alt="图片" style="zoom:67%;" />
        * 输出argv[0]的值
    * 执行上下两份代码的结果如下：
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114036.png" alt="图片" style="zoom:67%;" />
        * 可见，第二个参数体现在了argv[0]变量里
# 附加知识点

* 使用while(1){}时在循环体里加sleep，对CPU更友好
    * 否则可能导致CPU利用率蹿升、空转、过热
* pstree可以方便地看到进程的继承关系，-p可以显示pid
* 查看僵尸进程：ps、ps -aux、ps -ef、top均可
* 死锁：两个以上的运算单元，双方都在等待对方停止运行，以获取系统资源，但是没有一方提前退出
* 计算机中的同步与生活中的不太一样
    * 不是做同样的操作
    * 而是事件发生的顺序是确定的，是有因果关系的
# 思考点

# Tips

* du [-h]：查看当前目录以及所有子目录的大小 [human-readable]
* 对于多进程的输出，使用more会将不同进程的输出独立显示
* 推荐电影：《她》2013
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/mzJjSBN6d7KiV3Xl.png" alt="图片" style="zoom: 50%;" />
    * 一个硅基生命与许多碳基生命的爱情故事，涉及高并发概念
    * [豆瓣](https://movie.douban.com/subject/6722879/)；[百度云](https://pan.baidu.com/s/1ZYFlBXJSljUHGfF7fsMFhg)，提取码：8pic

---


