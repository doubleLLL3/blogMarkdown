---
title: '5 进程间通信'
date: 2021-01-19 18:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---

# 课程内容

IPC——进程间通信

## ——共享内存——

* 父亲生孩子之前约定好共享的数据位置
* 相关接口：shm*
## shmget

分配System V类型的共享内存段 [段式内存]

[PS] System V的通信方式，还在沿用；其启动方式已经被摈弃

* man shmget
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Fj7UKMezzrInWMQU.png)
    * 返回值类型：int
    * 形参类型：key_t，size_t，int
    * ipc→进程间通信
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/TUfmgAZhRlpjQQNt.png)
    * 传入的key和返回的id是联系在一起的
    * 新的共享内存段的大小，对应整数页大小 [上取整]
    * 创建共享内存需要有标志：IPC_CREAT
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/1y0xRVDsyG0YcFJC.png)
    * 如果没有IPC_CREAT，会检查用户对key对应段的访问权限
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/4gCFaQhwFVtpwSoA.png)
    * 注意：还需要通过至少9个bit明确权限，如0600，前面的0不能少
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/AESXdGXftJY4MEMx.png)
    * 成功，返回一个有效的内存标识符 [id]；错误，返回-1并设置errno
* 通过key拿到id后，如何找到对应的地址呢？shmat 👇
## shmat、shmdt

共享内存操作 [attach，附着；detach，分离]

* man shmat
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/ZfrIeX90GnykllA9.png)
    * 注意shmat的返回值为：void *
* 描述
    * **shmat**
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/ilyE7QfAdjjEAhlV.png)
        * 将id指定的共享内存段**附着**到调用进程自己的地址空间
            * 每一个进程都认为自己用的是一块独立的连续内存 [虚拟内存技术]
                * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/eiY0SvuG2BZRj1cu.png" alt="图片" style="zoom:67%;" />
                * 参考[Wikipedia](http://wikiwand.com/zh-hans/虚拟内存)
        * shmaddr明确附着地址：
            * NULL：系统自动附着 [**常用**]
            * 否则，附着自动下取整的地址 [shmflg中定义了SHM_RND]**或者**手动保证页对齐的地址
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/K3WPc4ve6wAHa5sC.png)
        * 成功的调用会更新共享内存的结构体shmid_ds
            * shm_nattach：附着数量。共享内存对应的真实物理空间可能被多个进程附着
    * **shmdt**
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/MF8oUNVBGr4OzBJK.png)
        * 是shmat的相反操作
        * 操作的必须是当前被附着的地址
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/M8APZidUyzlmbn3w.png)
    * 成功，shmat返回地址，shmdt返回0
    * 错误，返回-1，并设置errno
* id在整个系统中是唯一的，同一个id一定对应同一块**物理内存**
    * 【注意】相同的id通过shmat返回的地址不同，这是因为在不同的进程中表现为独立的地址空间 [**虚拟内存**的概念]
* 话说回来，shmget通过key拿到id，shmat通过id拿到内存地址，但【key】又是如何得到的呢？👇
## ftok

将一个路径名和一个项目标识符转换为System V IPC的key

* man ftok
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/3tjGSU6mTcsEpvMo.png)
    * 需要一个路径名和一个int类型变量完成转换
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/eKZiGoeXyMIt4iXH.png)
    * 文件需要存在，并可访问
    * proj_id有至少8个有效位，不能是0
    * 相同的文件名和proj_id对应相同的返回值
        * 所以固定的传入参数，返回的key是固定的
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/6BpWHT9X02LX15ts.png)
    * 成功，返回key；失败，返回-1，并设置errno
## shmctl

共享内存的控制

* man shmctl
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/jYGDPF9frVqK3XA9.png)
    * cmd：命令，int类型。可预测是一些大写的宏定义
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/TPk2BqaEyL7PeGwr.png)
    * 可以看到shmid_ds结构体的细节信息
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/1ZOuroCCrv5K1AbL.png)
    * 一些命令
        * IPC_STAT：拷贝shmid_ds结构体信息到buf中 [需有读权限]
        * IPC_SET：修改shmid_id结构体
        * IPC_RMID：标记段**将**被销毁
            * 只有当共享内存段**没有**附着时，段才会被真正的销毁
            * 不需要buf变量，设为NULL即可
            * [PS] 必须检查是否真的被销毁，否则可能会有残留 [可以通过返回值]
        * IPC_INFO [Linux特有，为了兼容性，尽量避免使用]
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/GDtyk5pkdvFdHT9g.png)
    * 一般地：0，成功 [除了INFO、STAT]；-1，出错
## ——线程相关——

来自线程库[pthread]

互斥量、条件变量

[PS] 多线程、高并发，使用共享内存时都需要考虑互斥

## pthread_mutex_*

【互斥量】操作mutex

* man pthread_mutex_init等
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/ARO88bpFF9GdCl7Y.png)
* lock：使用lock时，会判断是否已加锁，若加了锁，则会**挂起**直到解锁 [可能发生阻塞的地方]
* init：动态初始化方式，需要用到**属性变量**
* 属性接口：pthread_mutexattr_*
    * init：初始化
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/akwTZZmbDDkoZTm5.png)
        * 初始化函数中，attr是一个传出参数，返回值为0
    * setpshared：设置进程间共享
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/EOZDaA6OgWof0M03.png)
        * pshared变量为int类型，实际上就是一个标志位
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/a8ZH7bjWOXNlE4yQ.png)
        * 0：进程间私有；1：进程间共享 → 分别对应宏PTHREAD_PROCESS_PRIVATE、PTHREAD_PROCESS_SHARED
* 互斥量的基本操作：创建属性变量 👉 初始化互斥量 👉 加/解锁操作，详见代码演示——共享内存 [互斥锁]
## pthread_cond_*

【条件变量】控制条件

* man pthread_cond_init等
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/KmOiDDO1gTHF8KiW.png)
* 结构很像mutex
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/1fJvhVcfsQGIx5cV.png)
* 条件变量是一种同步设备，允许线程挂起释放处理器资源，直到满足某种条件
* 基本操作：发送条件信号，等待条件信号
* ⭐必须关联一个互斥量mutex，来避免竞争条件：可能在一个线程**准备好**等待条件之前，另一个线程就已经发出信号了，从而导致消息遗漏
* init：初始化可以使用属性变量 [但实际上在Linux线程实现中，已忽略属性变量]
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/sstMVedHaJaWf8ut.png)
* signal：只会重启正在等待的线程中的**1**个；如果没有等待中的线程，就啥事没有
* broadcast：则会重启所有等待中的线程
* wait
    * ① 解锁mutex并等待条件变量激活 [真正的原子操作]
    * ② 在wait返回前 [线程重启之前 / 等到了信号]，会重新将mutex上锁
    * [PS]
        * 在调用wait的时候，互斥量必须是上锁状态
        * 等待挂起的时候，不会消耗CPU
* ❗ 图中黄框所在段
    * 保证在wait解锁mutex->准备等待期间 [原子操作]，其他线程不会发信号
    * ❓ 那这个时候这个mutex已经解锁了，所以这个原子操作可能还需要用到一个类似互斥量的家伙
    * ❓ 感觉上wait可以先准备好等待->再解锁mutex，这是正常逻辑，而上面解锁了，又进行一个原子操作，那先解锁有什么讲究呢
# 代码演示

## 共享内存 [未加锁]

5个进程进行1~10000的累加和演示

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/J1JR9RUW1hFMDU7D.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/2caYTcA9NfEoBfKY.png)
* ❗ 这里是子孩子直接用从父进程那**继承**的共享内存地址share_memory [虚拟的地址]
    * 可见，不同进程中的相同虚拟地址都是指向同一片共享内存的 [物理内存]
    * 如果子孩子通过继承来的shmat和id获得共享地址，会对应子进程中新的虚拟地址，但是同样指向同一片共享内存，原继承的共享地址share_memory同样可用
* ❗ 如何在使用完共享内存后实现真正的销毁
    * 使用shmdt分离所有进程中附着的共享内存 [经试验，这一步没有也可以，因为进程结束会自动分离]
    * **再**使用shmctl配合IPC_RMID销毁共享内存段 [移除shmid]
    * [PS]
        * 每个进程会算一个附着，当附着为0时，shmctl才会真正销毁内存段
        * 也可以去掉shmget中的IPC_EXCL标志，不检测是否已存在，但不是根本方法
* 如果不进行上述的shmctl操作
    * 第一次执行没问题，代表成功创建了共享内存段
    * 但第二次执行显示文件已存在
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/quCRBHOcTr5YDT9E.png" alt="图片" style="zoom:67%;" />
        * 说明上次执行后，共享内存并没有自动销毁，第二次执行还是使用相同的key创建共享内存 [每次相同的key对应的shmid不相同]
    * [探索过程]
        * ipcs：展示IPC相关的资源信息
            * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/lR5T28tM9ZSKmXzC.png)
            * 可以看到未销毁的共享内存，还有消息队列、信号量
            * 以及它们的key、id、权限perms
            * 也可以看到它们的nattch都为0，说明已经没有附着了
        * ipcrm：删除IPC资源
            * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/LDnfkI8Yq4Q1XC5D.png)
            * 参数的使用通过--help查看
            * 可通过key或者id移除资源
            * 手动删除资源再运行程序：`ipcs | grep [owner] | awk '{print $2}' | xargs ipcrm -m && ./a.out`
* ❗ 出错如下错误的本质原因 [个人理解]
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/bX3o96VDjWGOgOUz.png)
    * 1~10000的正确累加和，应为50005000
    * 多个进程**同时操作**共享内存 [或者] 一个进程的读写操作未完全完成，另一个进程开始新的操作了。如：
        * 一个进程刚对now++，写入内存，此时另一个进程拿到新的now再++，再用加了2次的now和旧sum相加
        * 也就是now++和sum累加两个操作**不是原子操作**了
    * 但一般CPU速度太快了，所以很大概率进行的就是原子操作 [now++和sum累加写入内存]，很小概率出错
    * 多核比单核更容易出现计算错误，因为一个处理器只能同时运行一个进程
* [PS]
    * 设置权限时0600的第一个0表示使用8进制
    * usleep(100)：让进程休眠100ms，让一个进程计算一次就阻塞，让其它进程继续，单纯为了体现分工
    * 头文件根据man手册自行添加
## 共享内存 [互斥量]

在内存中更高效的锁，类似文件锁 [可前往[例子](https://doublelll3.ml/wxb_%E5%9F%BA%E4%BA%8E%E6%96%87%E4%BB%B6%E7%9A%84%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1_%E7%94%A8100%E4%B8%AA%E8%BF%9B%E7%A8%8B%E6%8A%A2%E7%AE%97%E7%B4%AF%E5%8A%A0%E5%92%8C/#%E5%8A%A0%E5%85%A5%E9%94%81)] 的思想

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/hbcN4ZQzgsI4bPmT.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/PDxJhD8UW5VRBohB.png)
* 创建互斥量：创建属性变量👉初始化互斥量
* 互斥量在进程间使用的两个条件
    * ① 互斥量变量放在共享内存中，共享给每个进程，从而控制每个进程
    * ② 在父进程中创建互斥量时，创建的属性变量设置进程共享 [默认只在线程间起作用]
        * 但现在一些内核可能不需要做此操作也可以，不过为了兼容性，建议设置一下 ❗
* [PS]
    * 计算完累加和后，也要记得解锁
    * 系统中最慢的操作是IO操作，所以在printf前解锁，让互斥量更早开放，会更高效
    * fflush可以手动刷新缓冲区，避免输出混乱
## 共享内存 [条件变量]

每个进程轮流算100次

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/bC44PLlbV10jgBEu.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/7b2a1wmcgY8K7fwJ.png)
* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/MzNdYzjqroXxsNH3.png)
* cond条件变量的初始化，类似mutex互斥量
    * Linux线程的实现，其实不需要attr属性变量；mutex同
* 对于单核机器，每次在发送条件信号之前，需要usleep一会，保证子进程准备好wait，等待信号。否则，
    * 对于父进程，其优先执行，发送信号，此时还没轮到子进程运行，就会错过信号
    * 对于子进程之间，也类似，发信号前，先让其他子进程先运行到wait状态 [实际上usleep也不能保证]
* ❗ 注意
    * wait前要有加锁的mutex
    * 每次操作完记得解锁+发信号操作 [100次计算 / 10000的累加完成]
    * 发信号操作的顺序有两种：
        * ① 加锁——发信号——解锁
            * 缺：等待线程由于收到信号从内核中被唤醒 [→用户态]，但是因为没有可加锁的mutex，很遗憾又 [从用户态] 回到内核空间，直到有可加锁的mutex
                * 两次上下文切换 [内核态和用户态之间]损耗性能。
            * 优：
                * 保证了线程的优先级 [❓]
                * 并且在Linux线程实现中，有cond_wait队列和mutex_lock队列，所以不会返回到用户态，从而不会有性能损耗
        * ② 加锁——解锁——发信号 [本文采用]
            * 优：保证cond_wait队列中的线程有可加锁的mutex使用
            * 缺：可能抢到mutex的低优先级的线程优先执行
        * 参考：[条件变量pthread_cond_signal、pthread_cond_wait](https://blog.csdn.net/lqygame/article/details/71124264?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)——CSDN
* 实现效果
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/4TuZlVMgJjsmehR7.png" alt="图片" style="zoom: 50%;" />
    * 每个进程轮流计算100次
    * [PS] 即使是usleep也不能完全避免**消息遗漏**，可以使用共享内存中的一个变量记录信号的发出；此外，还可能有**虚假唤醒**的情况发生
        * 综合解决方法，参考[虚假唤醒 && 消息遗漏](https://blog.csdn.net/liyupeng01boke/article/details/38511477?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)——CSDN
```c++
if (!count) {                   // ->消息遗漏 [还没有在wait状态的]
  pthread_mutex_lock(&lock);
  while (condition_is_false) {  // ->虚假唤醒 [同时唤醒多个]
    pthread_cond_wait(&cond, &lock);
  }
  //...
  pthread_mutex_unlock(&lock);
}
```
## 简易聊天室

* **chat.h**
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/8V72ODawqjnxOSmX.png" alt="图片" style="zoom:67%;" />
    * 用户名+消息+使用共享内存的标配
* **1.server.c**
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/mSGvwkpndJXRdlhh.png)
    * 逻辑与前面的代码演示基本类似；注意后面的清空操作，主要为了数据安全
* **2.client.c**
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Cdx63W0tq9j8Nt5r.jpg)
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/yBhM2ap61N00NV3T.png)
    * 关注第41行的while循环的作用：防止客户端抢锁，导致服务端收不到signal
        * 还存在隐患：可能某客户端抢不到锁，发生阻塞
    * [注意] gcc编译2.client.c时，一定要加-lpthread；否则不会报错，但运行时，服务端收不到信号
* 效果演示
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/gZWvxzoo24iENhlF.png)
    * 左为服务端，右为两个客户端
# 附加知识点

* 有亲缘关系的进程：父子之间、兄弟之间
# 思考点

* 进程之间抢着算 VS. 每个进程各算一百次，哪种方式更高效？
    * 后者更高效，往极端想，一个进程计算全部的更高效
    * 这涉及到CPU的吞吐量问题，这种累加属于CPU密集型，而不是IO密集型
        * 可参考4 高级进程管理——[处理器约束型和IO约束型进程](https://doublelll3.ml/wxb_4_%E9%AB%98%E7%BA%A7%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86/#%E5%A4%84%E7%90%86%E5%99%A8%E7%BA%A6%E6%9D%9F%E5%9E%8B%E5%92%8Cio%E7%BA%A6%E6%9D%9F%E5%9E%8B%E8%BF%9B%E7%A8%8B)
# Tips

* 查看IPC相关的资源信息命令：ipcs
* 对于用户线程，一个进程中的一个线程崩溃，整个进程里的所有线程都会崩溃
* 课前电影推荐：《与狼共舞》 1990
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/kb2eEJzDzrp9Z8HB.png)
    * 体验不同文化，有很多安静、美好的场景
    * [https://pan.baidu.com/s/1hqxmTAYyd3iCOyGdCTPmYw](https://pan.baidu.com/s/1hqxmTAYyd3iCOyGdCTPmYw)
    * 提取码：yqed

---


