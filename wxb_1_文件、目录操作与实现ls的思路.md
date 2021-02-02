---
title: '1 文件、目录操作与实现ls的思路'
date: 2020-12-31 18:00:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 文件操作

* 【引入】之前学过的cp、mv、cat命令，都涉及到文件的读写
    * cp：读→写
    * mv：读→写→删
    * cat：读→写
* 上面这些步骤是如何实现的呢？

---


【底层操作，基于文件描述符】

### open

打开或创建一个文件 [别名：openat、create]

* man 2 open【关注函数原型及描述】
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911974334-zRYB0s6vHFYCdwgV.png)
    * 返回值 int：文件描述符 或 -1
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911973333-ctFjfPc7ouEiJz3h.png)
        * 常用文件描述符：0-stdin、1-stdout、2-stderr
        * -1：发生错误，并会设置errno [可供perror使用，见代码演示]
    * flags：文件的打开方式
    * [PS] 不需要特意记头文件
* 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911971616-4NA5y5A8SjF884q2.png)
    * 系统调用 [system call]：帮助你做你没有权限做的事情
    * 如果open的文件不存在，可能会新建该文件 [当flags里定义了O_CREAT]
        * O_CREAT
            * open函数的flag
            * C语言体系中，全大写表示宏定义
            * 底层是一个int型数据，叫做位掩码
                * 32位，可表示32种状态，每一位表示一种状态
                * 状态之间可使用与、或、异或的方式转换
    * 文件描述符 [file descrip**tor**]
        * 小、非负、后续系统可调用 [read、write...]
        * 返回值永远是当前进程中可以取的最小的数字
            * 可用来判断文件数量 [如果返回1000，当前文件数量一定超过1000了]
    * 打开文件后，文件指针默认在文件头部
    * 文件描述 [file descrip**tion**]
        * 每次调用open会创建一个新的open文件描述，它是系统全局文件表中的一条信息
            * 记录文件偏移量和文件的状态
        * [PS] 文件描述符是一个open文件描述的引用，不因pathname的改变而受影响
    * ⭐flags
        * 必须包含O_RDONLY、O_WRONLY、O_RDWR中的一个
        * flag之间用位或组合
        * O_CREAT 创建
        * O_TRUNC 截断
        * O_DIRECT 直接IO
            * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911969217-ZwUuawUHEwSVCUBn.png)
            * 直接IO——同步写，文件会直接写进去，而不经过缓冲
            * 缓冲IO
                * 缓冲结束条件：① 攒到一堆数据；② 等固定时间
                    * 往磁盘写一个字符a，不会马上写入磁盘，可以减小成本
                    * 但断电时易丢失数据
                    * [PS]
                        * 磁盘的最小单元是块，每个块是4K
                            * 所以磁盘又叫块设备
                        * 类似printf输出到stdout的条件 [行缓冲]
                            * 遇到回车 / 程序结束，系统自动冲洗缓冲区
                            * 缓冲区满，自动冲洗
                            * fflush函数，手动冲洗
        * O_NONBLOCK 非阻塞IO
            * 阻塞
                * 如：scanf的时候，要等标准输入流中有输入才能进行后面的操作
                * 缺点：浪费资源
            * 非阻塞
                * 不会等
                * 缺点
                    * 需频繁回来查看，也浪费资源
                    * 需有某种机制监测，花费技术成本
        * O_TMPFILE 创建临时文件
            * 进程运行结束后文件就会被删除，交易关闭也会
            * 类似系统的临时文件夹/tmp
* ❗ 底层读写文件前，需要调用open函数获得文件描述符
### read

通过文件描述符读取数据

* man read
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911967104-ARruqOhO6IbihzZy.png)
    * 返回值 ssize_t：读取的字节数 或 -1
        * 以_t结尾，一般是用户自定义类型
        * 猜测：也是基本类型之一，可能是long long，可能是int
        * 通过ctags一步步找具体类型：ctrl + ] 、ctrl + o
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911965957-bNPLrqg6gvivUtqc.png" alt="图片" style="zoom:67%;" />
            * 答案：int [32位系统下]；long int [64位系统下]
            * [PS] 按理说，32位系统下，long int大小等同于int
    * buf、count：每次最多读取count字节数据到buf中
* 描述 + 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911964236-6RrznWn1uUE0tBWB.png)
    * 尝试读取最多count字节到buffer中
        * 读取字节数达不到count的情况：被人中断 [signal]；数据本身不足count大小
    * 每成功读取num [≤ count] 字节数据，文件偏移量 [像指针] 会自动往后走num大小
        * 如果文件偏移量在EOF [没有数据可读了]，函数返回0
    * count
        * 如果设为0，错误可能被检测出来，如果没有检测到错误，返回0
        * 如果大于SSIZE_MAX [int / long int的最大值]，返回的结果将是定义好的 [POSIX.1标准]
    * 返回值
        * ≤ count
        * 出错时返回-1，并会设置errno
* [PS] ERRORS
    * EAGAIN
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911962957-veHABIGS5EeQ9KSq.png)
        * 读文件 [包括socket] 的时候，尽管文件已被设置为O_NONBLOCK，read将会阻塞
### write

通过文件描述符写数据

* man 2 write
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911961924-vHVZPskO5EUQTa8L.png)
    * 与read很相似
* 描述 + 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911960571-NEfF2aAgOMe9ALXi.png)
    * 与read很相似
    * 写的字节数达不到count的情况：物理空间不够；系统资源限制；被signal中断
    * open文件时设置了O_APPEND [追加]
        * 文件偏移量 [offset] 在文件末尾，写操作会追加
        * 否则放在开头，写操作会覆写
### close

关闭一个文件描述符

* man close
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609912275739-ZjQOcPWhDLunI0Wk.png)
* 主要就是关闭文件描述符
* [PS]
    * 记录锁将被移除
    * 特殊情况
        * close的是文件描述的最后一个文件描述符，文件描述对应的资源会被释放
        * close的是文件的最后一个引用的文件描述符，文件将会被删除
    * 暂时不用在意内核具体做了什么

---


【标准文件操作，基于文件指针】

<stdio.h>

### fopen

通过流打开文件

* man fopen
* 原型
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911957565-vPIBzo161qeWiFIS.png)
    * 返回值 FILE *：文件指针
        * 早期是宏定义，这里大写是为了兼容性
    * mode
        * 类型是char *，而不是int
* 描述
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911954929-xrs9K7u1SYZKTKLI.png)
    * 关联一个流 [stream]
        * [PS] 网络上发布数据，字节流；文件流 <类型：FILE *>
    * mode
        * r / r+：读 / 读写
            * 流在文件开始
        * w / w+：读 / 读写
            * 流在文件开始
            * 文件存在，截断文件 [打开就会将原数据清除]
            * 文件不存在，创建文件
        * a / a+：追加 / 读与追加
            * 追加时，流在EOF；读时，流在文件开始
            * 文件不存在就会创建
        * +：读写通吃
        * [PS]
            * b：可以在mode字符串的最后或者两个字符之间，可用于处理二进制文件，但在Linux上一般没有效果
            * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911951494-qW3AKv8wbRfgRI1B.png)
            * ❓ 任何被创建的文件会被进程的umask value修正
* 返回值
    * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911943481-r9zpyy8CNm05gR7l.png)
    * 成功时，返回文件指针
    * 出错时，返回NULL，并设置errno
### fread、fwrite

二进制流的IO

* man fread / fwrite
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911941441-LUcrEhzmSWBtpySL.png)
* fread：从stream中读nmeb次数据 [size字节 / 次] 到ptr
* fwrite：把ptr的数据写nmeb次数据 [size字节 / 次] 到stream
* 返回值 size_t：读 / 写的items数量 [成功]
    * [**无符号**的ssize_t]
    * 发生错误或提前遇到EOF时 👉 0 ≤ 返回值 < nmeb
        * ❗ 所以不能通过返回值区分EOF和错误，需使用feof、ferror确认
    * [PS] 当size为1时，返回值等于传输的字节数
### fclose

刷新流并关闭文件描述符

* man fclose
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911940161-l9tRICTnHd417Fq2.png)
* 刷新流实际调用的是fflush
* 返回值
    * 0 [成功]
    * -1(EOF)，并设置errno [失败]
    * 未定义行为 [传入的是一个非法指针或已经被fclose过]
* ⭐标准IO里的所有操作都是缓冲IO
    * 本身没有权限写，需等待内核控制
* ❓ 标准IO更适合文本 [用户]，底层的IO更适合二进制文件
## 目录操作

本质上也是文件 [早期可以直接open]

### opendir

* man opendir
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911938619-rCcq6fwqGWAlpOFz.png)
* 返回值 DIR *：目录流指针 或 NULL
    * 目录流默认被放置在目录的第一个条目
    * 发生错误时，返回NULL，并设置errno
### readdir

* man readdir
* ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911937332-vm8yap2OtNzRcU4w.png)
* 返回值 struct dirent *：目录项 或 NULL
    * 目录流中下一个目录项 [结构体] 的指针
        * 结构体的主要字段：d_ino、d_name
        * [PS]
            * 一次一次地返回下一个文件
            * d_off：与telldir返回的值一样，又类似ftell()
                * 此offset [每个文件的大小不同] 与一般意义 [字节为单位] 上的不一样
                * ftell()获取当前文件所在位置指示器的值
    * NULL [遇到目录流结尾或发生错误时]
### closedir

* 关闭目录
## 实现ls -al的基本思路

* ls -al效果
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911935637-vkJUOIIpeyTfhPCD.png" alt="图片" style="zoom:67%;" />
    * 需要的信息有：文件权限、连接数、用户名、组名、文件大小、修改时间、文件名
* 思路
    * readdir()
        * man readdir
        * 读取目录里的每一个文件
        * 可获取文件名
    * stat()、lstat()
        * man 2 stat
        * 根据文件路径获取文件信息：stat结构体
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911933442-5HRval8N3GV68IXv.png" alt="图片" style="zoom: 50%;" />
        * 可获取文件权限、硬连接数、uid、gid、文件大小、修改时间
        * 可参考里面的EXAMPLE：lstat
        * lstat()和stat()的区别
            * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911932417-Bism4SyHRND7aByM.png)
            * lstat()可以查看软连接的信息，而不会跳到软连接指向的文件
    * getpwuid()
        * man getpwuid
        * 根据uid获取passwd结构体
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911931306-SMqGncUtToXAPLMa.png" alt="图片" style="zoom: 67%;" />
        * 可获取对应的用户名
    * getgrgid
        * man getgrgid
        * 根据gid获取group结构体
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911929420-SQ1i1Cg1Wq4Dz2xI.png" alt="图片" style="zoom:67%;" />
        * 可获取对应的组名
    * 如果自己去实现 → 读文件、切分
        * 用户信息：/etc/passwd
        * 组的信息：/etc/group
* 其他细节
    * 颜色
    * 排序
    * 纯ls命令输出的显示列数随宽度变化
        * 获取终端大小
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911928224-7JpZeaM1AjvUsHsl.png" alt="图片" style="zoom:67%;" />
        * 参考ioctl，man ioctl
    * 列宽如何确定，可用暴力方式、二分查找、慢慢逼近
# 代码演示

## 底层文件操作

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911926813-YMGqg3de6kJjvaMb.png" alt="图片" style="zoom:67%;" />
* ⭐ 详见注释，关注使用
* ❗ 避免乱码情况
    * 字符串buff末尾留一位放'\0'
        * sizeof(buff) - 1
    * 最后一次不足512字节的读取，需要排除多余字节的干扰
        * 方法①：手动menset(buff, 0, sizeof(buff))
        * 方法②：始终保持数据末尾是'\0'，buff[nread] = '\0'
    * [PS] 学习系统上层命令时，不必太关注这些
* perror 打印一个系统错误信息
    * man 3 perror
    * 原型
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911924962-CVun2mPup3OGLhFX.png)
        * fopen等发生错误时就会设置errno
    * 描述
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911924018-Ov0aVXAx5T85yPzT.png)
        * 在stderr上输出上一个调用的错误信息
        * s通常包含函数的名称
* 建一个常用的common头文件夹，放常用的头文件
    * head.h
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911922932-SYoNt70o6NN7oK5i.png" alt="图片" style="zoom: 67%;" />
## 标准文件操作

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911921001-KhG3ZVzuHu3GtoSS.png" alt="图片" style="zoom:67%;" />
* buffer放在循环里，每次都会初始化
* nread为非负数，并且不能区分EOF和错误
## 标准IO是缓冲IO

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911920001-L87o6NXvF6ArZMLc.png" alt="图片" style="zoom:67%;" />
* 第一个"Hello world"直接输出，stderr没有缓冲
* 第二个"Hello world"本来会等sleep结束，无法输出到stdout，但是可以立马输出，通过👇
    * 手动刷新缓冲区：fflush
    * 输出换行
* sleep函数在unistd.h中
# 附加知识点

* ulimit -a，可查看可打开的文件数量上限
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911918467-G3C0Xt336prYucP0.png" alt="图片" style="zoom: 67%;" />
    * 每个进程中文件打开数量上限为1024
        * 超过会使系统崩盘
        * [PS]
            * 系统崩盘还需考虑内存
            * 要做一个负责任的程序：手动close / free、输出错误日志
* 只有标准输出是行缓冲的
# 思考点

* ❓ 文件保存就会立即写入磁盘吗？
    * [参考Are file edits in Linux directly saved into disk?](https://unix.stackexchange.com/questions/464184/are-file-edits-in-linux-directly-saved-into-disk)——StackExchange
# Tips

* 在vim中，Shift + K可跳到man手册
* 推荐复制即翻译软件：[CopyTranslator](https://copytranslator.github.io/)
* man手册在线文档：[man page](https://linux.die.net/man/)——die.net

---


