---
title: '3 Linux基础知识'
date: 2020-11-22 18:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 由图形化[Windows]转为字符界面[Linux]

### 错误与提示

图形化→文本信息

* 一定不要忽略屏幕输出，仔细看系统的每一个回复！
* 程序return值反应在下一行右侧
    * ![图片](https://i.loli.net/2020/12/09/kGLOnr9hC6Jdamw.png)
    * return 0 表示成功

### 命令格式

命令 [选项] [选项参数]... [参数]...

* 选项：--选项全称 -选项简称
* 空格：不管多少个都是一个空格，作为分隔符

### Linux在安全方面考虑更多

用户、用户组、权限

* 修改密码：passwd
* 退出用户：exit、logout、ctrl + d
* 新建用户：useradd
* 家目录：/home，其他用户在其他目录可能没有修改权限
* 组
* [PS] 一切皆文件，一切设备、抽象的进程、运行数据、CPU...
    * 与用户相关的文件 /etc/passwd、/etc/group，相关命令 usermod、userdel、id
    * id：可查看自己或指定用户的uid、gid(主要组)、groups(所在组)

### 用户和组

* ![图片](https://i.loli.net/2020/12/09/BPT6DwbS4hq3QJv.png)
* **权限**：字符表示(r可读、w可写、x可执行)→8/10进制描述
* 每个文件都有一个拥有者、一个拥有组、其余用户
* 输入ls -l可查看
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/og2lBeqydMNErhC1.png!thumbnail" alt="img" style="zoom:67%;" />      
    * 见红框，文件类型l、d等，详见附加知识点-7种文件类型

### 浏览文件

Linux只有一棵树，起于根目录 /

### 我是谁、我在哪、我要去哪

* 我是谁 whoami、who am i（看到本质）
    * <img src="https://i.loli.net/2020/12/09/ltaNbQJdpjOHsvW.png" alt="图片" style="zoom:67%;" />
    * whoami：系统感觉你是谁，who am i：你实际上是谁
* 我在哪 pwd：print working directory
* 我要去哪 cd：change directory
* [PS] 书写命令或路径时，善用Tab键

### 软件的安装（Ubuntu）

* .deb：使用dpkg -i xxx.deb
* .tar：使用tar解压
* **apt安装**：可自动解决依赖关系、可选择合适的软件源地址
    * 安装到哪去了？分散放置的：bin、lib；/usr/→bin、include、lib、local、share(帮助手册)、src
    * 相关命令
        * <img src="https://i.loli.net/2020/12/09/jLIMvfm6zhAWqeN.png" alt="图片" style="zoom:67%;" />
        * search可以找一个软件，查看是否存在、可能忘记名字了
        * apt remove xxx--purge (不保留配置文件)
        * apt autoremove慎用，毕竟是apt管理的，可能误删还要用但没在运行、其他方式下载的软件

## Linux常用命令

### 文件及目录操作

* <img src="https://i.loli.net/2020/12/09/VWNkg9dzbjhe2iu.png" alt="图片" style="zoom:67%;" />
* ln：软连接、硬连接
### 文件内容的修改与查看

* <img src="https://i.loli.net/2020/12/09/DrmvGtY37AsdU5h.png" alt="图片" style="zoom:67%;" />
* ⭐Linux三剑客：grep [Global Regular Expression Print]、awk [数据处理]、sed [批量操作]
* ❗ 管道：|  把前一个命令的输出传给下个命令
* less比more更友好，功能更多
* wc：word count，可接参数-l [行数]等等
### 文件的查找与定位

* <img src="https://i.loli.net/2020/12/09/cyagNJAMbr8V45F.png" alt="图片" style="zoom:67%;" />
* which：查找可执行文件的具体位置，具体用的是哪个
* locate
    * 基于索引，查找非常快
    * 【不是实时更新】，因为更新需遍历全部文件，慢
    * 会定期更新，或使用sudo updatedb立即更新数据库
### 用户相关

* <img src="https://i.loli.net/2020/12/09/loiRHM7E45K9Uzv.png" alt="图片" style="zoom:67%;" />
* sudo -i：使用超管权限登录默认shell
* chmod：change mode
* chown：change owner
### 进程相关

* <img src="https://i.loli.net/2020/12/09/7qambg5TtjuzKMJ.png" alt="图片" style="zoom:67%;" />
* 常用ps -ef，输出所有进程的详细信息
* kill指定PID [process ID] 即可杀死进程
* pkill可以批量杀死字符匹配的进程，只杀死该用户有权限杀的，sudo pkill慎用
* crontab -e：编辑【定时任务】，参考里面格式即可
* ctrl + z、fg、bg、jobs
    * jobs查看进程序号
    * ctrl + z会让进程暂停
    * bg可以在后台跑，输出会显示在终端
    * %2（或fg %2）[zsh下]、fg 2 [bash下] 将2号进程调至前台
    * [PS] fg可用在修改源文件、gcc的时候，提高效率
### 获取系统信息

* <img src="https://i.loli.net/2020/12/09/Lgstr5SnFNh2QPb.png" alt="图片" style="zoom:67%;" />
* 可用于调优
* 查看有没有被别人占用资源，检查系统是否被攻破：top、htop、nmon
* 【如何降低云主机被攻破的几率】
    * 取消密码，配置sshd，通过公私钥方式登录
    * 改复杂密码
    * 改默认的用户名或新建一个用户
    * 改ssh的连接端口
* -h 说人话，友好显示
    * du -h，友好显示目录文件大小，K、M
    * free -h，友好显示内存数据
* nmon：适合检查系统什么时候出了问题
* ifconfig [interfaces config] 显示的[网络设备信息含义](http://einverne.github.io/post/2017/10/ifconfig.html#%E6%98%BE%E7%A4%BA%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E4%BF%A1%E6%81%AF)-博客
    * 能看到私网ip，不能看到公网ip
* uname -a：打印所有可用的系统信息，还可以用cat /etc/os-release
### 其他命令

* <img src="https://i.loli.net/2020/12/09/2GIXykMsfNReqlJ.png" alt="图片" style="zoom:67%;" />
* 在ssh [已开源] 以前常用telnet
* 【scp使用ssh拷贝】
    * 远程👉本地：scp username@ip_address: 远程文件 本地路径
    * 本地👉远程：scp 本地文件 username@ip_address: 远程路径
* 云主机用了poweroff后，需连到控制台去开机
## 基础知识总结

### Terminal & Shell

* Terminal类似设备
* Terminal里面运行着Shell软件，比如bash、zsh，用来解释在Terminal中输入的命令
* Shell接收命令时：是否内置→在系统环境变量PATH中的路径里查找、调用
### 分隔符

* 一些特殊符号也属于分隔符：管道 | ，重定向 >、>>、<、<< ，后台运行 &，序列执行 &&
    * 重定向到/dev/zero、/dev/null：输入到数据黑洞，输入到此处的东西直接被忽略，具体通过man zero查看【/dev：设备】
### 程序与进程

* 程序就是一个可执行的二进制文件；进程是程序在内存中的镜像、实例化
### 路径

* 绝对路径：起始点为根目录/
* 相对路径：善用当前路径"."和上一级路径".."
* 远程路径：协议://用户名:密码@位置/路径:端口
* 特殊路径：~用户名 = 用户名的家目录，- = 上次工作目录
### 软件

* Linux中没有注册表概念
* <img src="https://i.loli.net/2020/12/09/S3TdJpfgOtP8uX4.png" alt="图片" style="zoom:67%;" />

### 隐藏文件

* 名字以.开头即可
* 特殊目录：当前目录"."和父目录".."
### 文件类型

【7种文件类型】

* <img src="https://i.loli.net/2020/12/09/czgy186mWStRKpq.png" alt="图片" style="zoom: 80%;" />
* link：可暂时理解为windows的快捷方式
* block：系统大多数的block是4096Byte = 4KB，起到缓冲的作用，类比快递配送
* character：设备也是文件，如虚拟终端 /dev/pts/0
* socket：基于网络的都离不开它
* pipe：不占用内存，只负责传输，举例一次传输：echo > 管道文件，cat 管道文件
* 3种普通文件
* <img src="https://i.loli.net/2020/12/09/dcJIQ5OxYHSnw9G.png" alt="图片" style="zoom: 50%;" />

### 文件权限的修改

* chmod
    * 善用+、-、=
        * ![图片](https://i.loli.net/2020/12/09/vEDJf5HhxFLP4s3.png)
        * a = all、+ 增加权限、- 去除权限、= 直接覆写权限
    * 尽量减少chmod 777的使用，权限开放过多
* chown
    * 可同时修改文件所属的用户和组
        * chown 所属用户:所属组 文件
    * 可修改目录及目录下所有文件的所属用户
        * chown -R 所属用户 目录
* chgrp：修改文件所属的组，一般可用chown替代
* 查看文件权限
    * ll = ls -lh
        * 可通过which ll或alias ll查看ll具体使用的命令
    * l = ls -lah
    * 系统中的三个时间：修改时间mtime、读取时间atime、权限修改时间ctime
### 用户

* 慎用root
* su 用户名：切换到另一用户，需输入其密码
* su - 用户名
    * 使用"-"，会完全更新环境变量，适合复杂操作
    * 不加"-"，会适当更新环境变量，适合临时切换用户
* 不加用户名，则默认切换到root
# 附加知识点

* . 当前目录 .. 父目录 / 根目录
* 僵尸进程[有害]：子进程死了，父进程没管，子进程还占着资源；孤儿进程[无害]：会被1号进程领养
* 与其它终端聊天
    * 输入w查看在线终端
        * ![图片](https://i.loli.net/2020/12/09/hDpCbj54OFMsQ98.png)
        * TTY[Teletype]：终端；pts[pseudo terminal slave]：虚拟终端
    * 输入echo "hello" >> /dev/pts/1给终端pts/1发"hello"
        * ![图片](https://i.loli.net/2020/12/09/pxiEV9wtQFdWZBS.png)
        * pts/1可以收到消息，并以同样方式echo回信
    * 还可以使用wall "System is rebooting in 15s"告诉所有在线用户自己要重启了
* sudo !! 可以以超级用户准备执行上次命令
    * ![图片](https://i.loli.net/2020/12/09/P3qp2SBJFWL8yDV.png)
    * 可以以超级用户准备执行上次命令，不需要再输一遍命令了
* whereis
    * ![图片](https://i.loli.net/2020/12/09/Qgjh3exSyTvfR1A.png)
    * 查看软件的安装目录
* file 文件
    * ![图片](https://i.loli.net/2020/12/09/S8dyhzKCnvpIJUg.png)
    * 查看文件的具体信息
* 通常使用[man 命令]或[命令 -h]可以查看命令帮助
    * 使用说明的格式规则：[] 可选选项，| 不能同时使用的参数
* 目录也是文件
* alias 别名=命令，可设置别名对应的命令
# Tips

* 用户相关
    * 创建用户时没有特殊指定组，会以自己用户名创建一个组
    * 一个用户可以在多个不同的组
* 优秀的资源监控软件
    * Htop：一款运行于Linux的监控与进程管理软件，比top更友好
    * nmon：用于Linux的计算机性能系统监视工具 [IBM开发]
    * dstat：灵活的资源统计工具
* 命令相关
    * apt和apt-get没区别
    * history：打印输出的命令历史
    * tldr + 命令：查看命令格式
    * echo $PATH：打印PATH环境变量，存放系统默认的可执行文件的路径
* [CPU状态信息us,sy,ni,id,wa,hi,si,st含义](https://blog.csdn.net/Sasoritattoo/article/details/9318893)-CSDN
* [如何在 Ubuntu 上修改主机名](https://www.itcoder.tech/posts/how-to-change-hostname-on-ubuntu-20-04/)-博客，关于hostnamectl
    * <img src="https://i.loli.net/2020/12/09/jRKHp5QWTd9baxU.png" alt="图片" style="zoom:67%;" />
    * 基本操作可参考 [5 基本系统——网络——第2点](https://doublelll3.ml/lnxrm_5_基本系统/#网络)

* 计算机网络学习推荐：[《计算机网络：自顶向下方法》](https://github.com/Epsirong/book/raw/master/os/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%E6%95%99%E7%A8%8B%EF%BC%9A%E8%87%AA%E9%A1%B6%E5%90%91%E4%B8%8B%E6%96%B9%E6%B3%95.pdf)，重点前5章

---


# 课程速记


