---
title: '4 命令系统'
date: 2020-11-25 18:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## Shell & Terminal & Console

* shell：壳，软件，给用户提供一个接口
* console：控制台、工作台
* terminal：交互设备
    * 本质就是文件
    * ![图片](https://i.loli.net/2020/12/09/dWhqRLDSAfgnoKJ.png)
    * stdin、stdout、stderr分别对应0、1、2号文件
    * 0、1、2是文件描述符，打开任何一个进程，都会打开这3个文件
    * echo $0：输出-zsh，说明输入来源是zsh
    * stdout、stderr输出重定向：1>文件，2>文件
>console 和 terminal的概念都源自大型机，console可以看作为一个特殊的terminal。现在用的一般都是引申义，一般情况下可以混用。
## Linux帮助系统

* 两个在线文档：man(常用)、info
* man手册模块

|代号|代表的含义|举例|
|:----|:----|:----|:----|:----|
|1|shell命令或者可执行文件|man 1 ls|
|2|内核提供的函数和工具|man 2 reboot|
|3|库函数 [大部分C的函数库，没有C++、Python...]|man 3 readdir|
|4|设备文件说明 [通常在/dev]|man 4 null|
|5|配置文件或文件格式 [如/etc/passwd]|man 5 interfaces|
|6|游戏|man 6 lol|
|7|惯例与协议 [如Linux文件系统、网络协议]|man 7 tcp|
|8|系统管理员命令 [通常给root使用]|man 8 reboot|
|9|内核例程 [非标准例程]|    |
|o|旧文档|    |
|n|新文档|    |
|l|本地文档|    |

* 学会看man手册提供的Example👉最佳实践
* ❗ 小技巧，针对命令可能存在于多个模块
    * 关键字查找：man -k reboot
    * 精确查找：man -f reboot
## zsh

### 通配符

* ? 单个任意字符
* \* 任意几个任意字符
* []、{}

|通配符|含义|
|:----|:----|
|[123]|匹配123中的任意单一字符|
|[1-3]|匹配1-3中的任意单一字符|
|[0-9a-zA-Z]|匹配所有数字、大小写字母中的任意单一字符|
|[!(1-3)]|匹配除1-3的任意单一字符，在bash中可以不加括号|
|{"a","ab",...}|匹配"a"或"ab"(或更多)其一字符串，不能有空格，至少有两个元素|

### 任务管理

* & 命令后用可让其后台执行
    * 如何结束？
        * ① fg→ctrl + c
        * ② kill 任务id (在执行的时候可以看到)
        * ③ pkill 匹配名字 (注意权限过高，可能误删)
        * 在输入kill时，终端的输入和输出是混在一起的，但其实它们来自不同的文件：#0、#1
* ; 放在命令之间，顺序执行
* && 逻辑与，注意短路原则
* || 逻辑或，注意长路原则
* \` \` 命令替换符(注意：这个键来自于esc下面)
    * 优先执行其中的命令，再把结果给父命令
* ctrl + z 挂起任务，至少会释放CPU资源
    * 内存是否释放：得看底层怎么置换，一般是内存不够时置换到交换区
    * 和[sleep命令](http://c.biancheng.net/linux/sleep.html)差不多
* bg、fg、jobs：详见《Linux入门及使用》笔记汇总——3.Linux基础知识——进程相关
### 重定向

* [命令] >/>> [文件]
    * 注意：>>将内容**追加**到文件的末尾，而>内容覆盖原文件
* [命令] < [文件]
    * 将文件的内容给命令作为输入
* <<
    * 用来在输入时指定文件结尾
        * <img src="https://i.loli.net/2020/12/09/aYeOCBknJQF8slX.png" alt="图片" style="zoom:67%;" />
        * 这里EOF和000只是字符串，没有特殊含义
### 转义符

* 硬转义
    * 单引号包裹 ' '
    * 不管什么字符，都是所见即所得
    * 【注意】包裹里不允许还有单引号
* 软转义
    * 双引号包裹 " "
    * 除了特定的shell元字符（$用于变量值替换、`用于命令替换、\用于转义单个字符），其他的都是都是所见即所得
* 反斜杠
    * 转义，去除其后紧跟的元字符或通配符的特殊意义
# 附加知识点

* 变量调用：$Var 等价于 ${Var}
    * 但后者更标准，约定了$的作用范围，可以避免变量名里有特殊字符导致问题的情况
# Tips

* ls --time=[atime、ctime] -l可以选择显示访问时间atime或权限修改时间ctime，而不是默认显示的修改时间
    * 注意：配合-l使用
* 对于有空格的文件名，zsh可能会产生误导，如下：
    * <img src="https://i.loli.net/2020/12/09/w9NfMFbZQJSYIam.png" alt="图片" style="zoom:67%;" />
    * 这里单引号是为了包住空格，实际上并不存在！
    * 若要批量删除，使用sudo rm -i *\ *
        * 这里用 -i 开启了询问模式，因为这个 * 和 \ 令人生怕

---


# 课程速记

