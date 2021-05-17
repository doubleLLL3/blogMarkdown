---
title: '5 基本系统'
date: 2020-11-27 18:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## 目录结构

*  <img src="https://i.loli.net/2020/12/09/jLbYP79MTSJ82vc.png" alt="图片" style="zoom:67%;" />

*  <img src="https://i.loli.net/2020/12/09/ynqKsTgSC764vxQ.png" alt="图片" style="zoom: 67%;" />

* 最上头的root为根目录/，不是root用户
* 此外，在根目录/下还有一个root文件夹，是root用户的家目录
    * 除了root用户，其它用户都无法进入
* bin、sbin 二进制/[系统的]
* **etc** 配置文件夹【全局】
* opt 可选的，存在意义不是很强，用户可选择地放文件
* run 当前正在跑的文件，一般不关注
* **var** 动态数据
    * mail 系统的通知机制，不是e-mail
    * log 日志【重要，勿修改】
        * 查看信息一般需要sudo权限，一般要查看error、warning的位置
        * last显示的登录信息就存放在wtmp文件中
* boot 内核文件，启动相关
* **home** 所有用户的家的父目录
    * 用户的家都在home下，路径为${HOME}或~
* lib、lib64 库
* media 媒体 [早期挂载软盘、光驱用，U盘不在这，现在都用得少了]
* mnt 挂载目录
    * 挂载其它的文件系统，如U盘、WSL中的CD...盘
    * 约定俗成的挂载点，不会自动挂载，也可以有其他任意位置的挂载点
* **proc** 进程
    * 通过ps查看对应进程id后，可在proc目录下找到对应id的目录
    * 目录里的文件大都是0KB，因为都不是真的，只是为了显示
    * 其中有一个fd目录→文件描述符file descriptor
        * <img src="https://i.loli.net/2020/12/09/wz8Cq25sFU7girL.png" alt="图片" style="zoom:67%;" />
        * 可以往stdout【#1】输入abc，即会在终端显示
        * 暂时不管10是做什么的
* tmp 临时文件夹
    * 关机就会清空，不是缓冲
    * [PS] tmpfile命令，可创建临时文件，参考man tmpfile
* dev 设备文件
* **usr**用户主动安装的文件
    * 一般是管理员用户，普通用户无法安装软件
    * local 共享文件[手册...]
## 启动流程

* 👉按下开机键
* 👉加载**BIOS**：硬件检测[CPU、磁盘、内存条等]，读取配置[系统安装位置等]，直到读取MBR
    * BIOS——基本输入输出系统，基于CMOS芯片的固件
        * 固件是基于软件和硬件的，将一些程序固化[烧制]在一个芯片上
        * 一直上电的[纽扣电池]，掉电就会初始化
* 👉读取第一个可启动设备内的MBR [主引导分区]
    * 存储了引导程序 Boot Loader
    * 加载kernel：检测硬件，加载驱动
    * 此时kernel接手BIOS的工作
* 👉执行引导程序 [Ubuntu：**Grub**]
    * 加载**虚拟文件系统**，把内核kernel跑起来
* 👉初始化磁盘，读取系统镜像文件
* 👉**启动1号进程**
    * 早期叫init，现在叫cnd
    * 此时系统有自己的功能了
* 👉加载磁盘，**挂载数据**，**启动各种服务**，连接终端
* 👉【最后】**运行x windows系统**→有图形化界面
### 运行级别

Linux下，不同的运行级别对应不同的服务来启动系统

|run level|含义|备注|
|:----|:----|:----|
|0|halt|系统关机|
|1|single user mode|在系统出问题时维护用，类似安全模式|
|2|multi-user, without nfs|nfs：network file system|
|3|full multi-user mode|完整的多用户纯文本模式【常用】|
|4|unused|系统保留，没有用|
|5|X11|在run level 3的基础上加载X windows|
|6|reboot|重启|

* 启动方式发展
    * System V [依次启动]
    * Upstart [分组启动，没有依赖关系的服务可以同时启动]
    * Systemd[全部服务一起启动，有依赖关系的服务再稍稍滞后，进一步提高并发性]
* 系统启动速度并不代表系统性能的好坏，如大型机器，几年重启一次，要好好检查一下硬件
* 热启动：在没有必要的情况下，跳过BIOS的硬件检测过程，加快启动速度
## 配置文件

### 文件系统

* /etc/fstab 开机时挂载的文件系统
    * 静态文件系统信息，指示需要挂载的系统盘
    * 如：需给公司机器挂载一个其它的磁盘，可以在这设置
* /etc/mtab 当前挂载的文件系统
### 用户系统

* /etc/passwd 用户信息
    * 不仅有普通的用户，还有非常多系统用户
    * 用户:密码占用符:uid:gid:用户描述:家目录:默认shell
    * 以前密码占用符存放密码，现在放在👇
* /etc/shadow 用户密码
    * 显示的密码加密了，解密成本也许大于获取的价值
* /etc/group 群组信息
* /etc/gshadow 群组密码
    * 基本不用，root来管整个体系即可，没必要细分到组
* /etc/sudoers Sudoer列表
    * %代表组
    * 可自己添加
### Shell

* echo ${SHELL} 查看该用户的Shell类型
* /etc/shells 可用的Shells列表

【针对zsh】

* /etc/zsh/zprofile 用户首选项【全局】
* ⭐在man zsh里搜索——FILES，即可看到所有可配置zsh的文件路径
    * 举例：在4个全局配置、4个用户配置里echo "In ..."
    * 在用户登录zsh时，显示顺序如下：
    * <img src="https://i.loli.net/2020/12/09/fZXuSCQa3kc72Kx.png" alt="图片" style="zoom: 50%;" />
    * 顺序：zshenv→zprofile→zshrc→zlogin，先全局(G)，再用户(L)
    * [PS] 用户配置文件
        * $ZDOTDIR/.zshenv
        * $ZDOTDIR/.zprofile
        * $ZDOTDIR/.zshrc
        * $ZDOTDIR/.zlogin
        * $ZDOTDIR/.zlogout
        * $ZDOTDIR——默认为用户的家目录
### 系统环境

* /etc/environment 环境变量
    * PATH环境变量：${PATH}
    * which 命令，会在这【PATH环境变量】里面的路径找命令对应的文件【文件需可执行】
    * ⭐PATH添加路径"."：PATH=${PATH}:.
        * 可直接在PATH变量后添加:.，即可连接
        * 还可使用export PATH=${PATH}:.
        * ❗ 但两种方式都是在内存中修改，重连shell后就会消失
    * 详见下2节——环境变量
* /etc/updatedb.conf 文件检索数据库配置信息
    * updatedb 更新数据库 [更新后，可以使刚被创建的文件被locate找到，数据库不是实时更新的]
* /etc/issue、/etc/issue.net 发行信息、[远程登录时的显示]
* /etc/os-release 更详细的系统信息

![图片](https://i.loli.net/2020/12/09/k9wEGshH8myqrLt.png)

### 网络

* ⭐/etc/hosts 主机列表
    * 也叫静态DNS，可以写对应关系
    * ❗ 域名[主机名]→IP地址
    * [自己的理解]类似windows的hosts，对于一个名称的解析先找hosts，再使用DNS
* /etc/hostname 主机名
    * ①需要sudo权限修改→新的主机名
    * ②**重启主机**；或者使用[hostname 新的主机名]**临时修改内存**中的主机名，否则改了后，环境里的主机名并没有变
    * ③再重连即可
    * [自己的理解] 第①步改的是真正的主机名，第②步hostname用来临时修改内存里的主机名，直接重启也可刷新内存
* ⭐/etc/resolv.conf 域名解析服务器地址
    * DNS 动态域名服务器
    * ❗ 域名→IP地址
* /etc/network/interfaces 网卡配置文件，先自行查看
## 环境变量

* env 所有环境变量，命名通常为大写字母
    * 通过$加上环境变量的名称，即可调用
    * PATH 可执行文件夹的路径
    * OLDPWD 上次工作目录
    * HISTSIZE 保存的历史记录大小 [输入命令]
        * bash默认保存1000行
        * zsh暂时没有该变量
        * 通过history可以查看输入的命令历史，无痕浏览自行搜索
* export
    * export PATH=${PATH}:.
    * 等同于PATH=${PATH}:.
    * 相比直接给环境变量赋值，export更规范
    * 这是在内存中修改，如果想固化该变量，每次连接都生效，可在任意【配置文件】中添加上述语句，zsh的配置文件详见上3节——Shell
* [PS] 可通过set查看所有本地定义的环境变量
## 软件管理

* 源码安装
```shell
git clone XXXX  # 下载源代码
cd XXX
make            # 编译
make install    # 安装
```
* 软件包管理系统
    * ⭐推荐apt：它可以被理解为apt-get和apt-cache的高级集成版
    * man apt可以看到其描述：高级、比apt-get、apt-cache交互性更好
    * ![图片](https://i.loli.net/2020/12/09/sLdZyrEwxCpcTSV.png)
    * 举例：
        * <img src="https://i.loli.net/2020/12/09/CbnwDm5BjvK71hf.png" alt="图片" style="zoom:67%;" />
        * 详见：[APT Vs APT-GET: What's The Difference?](https://phoenixnap.com/kb/apt-vs-apt-get)
# 随堂练习

# 代码演示

# 附加知识点

* shell中声明定义的变量存储在内存中，在进程空间里，将shell关闭再打开，变量就没了
    * 不要把一切皆文件的概念理解过头了，文件是静态的，需要进程来维护文件，使动态化
    * 变量存储在进程空间里
* ps可以看到进程ID
* ⭐使用ssh 绰号登录云主机，如ssh Ten，WSL上效果如下👇
    * <img src="https://i.loli.net/2020/12/09/XjOHIKAUpQkdnlz.png" alt="图片" style="zoom:67%;" />
    * 针对WSL或MacOS
    * ①在本机的/etc/hosts里添加云主机的IP，对应一个绰号Ten，如：45.123.111.1 Ten
    * ②在本机创建一个用户，名称与想登录的云主机用户一致，如：hz[本机用户]、hz[云主机用户]
    * ③此时在本机ssh Ten，输入密码即可登录
    * ❗ 重启后失效 [针对WSL 2，其他系统方法自行搜索，WSL 1不能chattr]
        * 使用sudo chattr +i /etc/hosts，使/etc/hosts只读，重启也不会被重置
        * 若要再修改：sudo chattr -i /etc/hosts
    * [PS] 还可使用ssh-copy-id做免密登录，tldr ssh-copy-id可查看帮助说明
* 域名和IP可以是多对多的，可以做负载均衡
# 思考点

# Tips

* [bash下] /etc/profile 用户首选项【全局】 [默认shell为bash时会先运行的文件]

---


# 课程速记

