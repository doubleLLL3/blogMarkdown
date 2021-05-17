---
title: '1 揭开Linux操作的神秘面纱'
date: 2020-11-18 18:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
*  ![image-20210104202713050](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609763235721-image-20210104202713050.png)

# 课程内容

## 阶段课程介绍

### 什么是Linux

* Linux发展：Unix→Mac→Windows→Linux
* 典型的Linux：Ubuntu乌班图、RHEL红帽企业版、CentOS（红帽的小白鼠）、Suse

*  <img src="https://i.loli.net/2020/12/09/slHDde3L1yk58iP.png" alt="图片" style="zoom: 80%;" />

PS：deepin在Debian下

* 是什么？为什么用？
### 简单的使用Linux

* 怎么用：学习方式、通用模式
* 怎么查：man手册、[tldr](https://github.com/tldr-pages/tldr)-Github（开源、精简）
>man + [要查的命令]
* 文件及目录操作、系统信息获取、配置软件、优化系统
* ⭐使用Linux编程、开发、搭建服务
### 常见命令

* 50+
* ~~删库跑路~~：rm -rf /*
    * 参考[写了Bug，误执行删库代码——如何预防误删库](https://www.cnblogs.com/xiaolincoding/p/12809812.html)-cnblogs
### Shell编程

* 脚本是什么，类比电影的剧本
    * 与C程序有本质区别，C是处理数据的，Shell是把很多流程糅在一起
    * 与脚本语言Python比，Shell是Linux自带的接口，直接联系，可直接调用
* 掌握：变量、输入输出、分支、循环、⭐数组
## 操作系统简介

### 什么是操作系统

* 用户与计算机、计算机硬件与其他软件的接口，就像一个大管家
* Linux系统：内核是Linux的所有操作系统的简称

*  <img src="https://i.loli.net/2020/12/09/5zgjF83WLsflNHD.png" alt="图片" style="zoom:80%;" />

* 整体架构
    * 硬件级（看得见摸得着）
    * 内核级（文件子系统、进程控制子系统等）
    * 用户级（C语言库等）

*  <img src="https://i.loli.net/2020/12/09/IEheNx1p2LZzwYo.png" alt="图片" style="zoom: 80%;" />

### Linux历史

*  <img src="https://i.loli.net/2020/12/09/YZjeQzGDJfS7xRC.png" alt="图片" style="zoom: 33%;" />


**【操作系统】**

* 1941年：有了第一台计算机
* 1965年：大而全的Multics，几家大公司实验室
* 1969年：unics，肯·汤姆逊（为了游戏，脱胎于Multic）
* 1973年：C语言，重写Unix，丹尼斯·里奇
* 1984年：Minix，谭邦宁
* 1991年：Linux，Git，⭐李纳斯Linus（为了游戏，大三）
    * 谭邦宁和李纳斯目前还经常互动
    * Linux内核代码量：10万行（1993）👉2780万行（2020）
    * Linux设计原则：满足一部分用户，不同系统有不同的用户偏向【开源】
    * Linux/Unix在国内服务器操作系统的占比：86%，2016年
    * Windows服务器一般在国企、事业单位常见，代码、系统可能都是几十年前修修补补出来的，只要稳定就行，不敢变动

[硬件发展]

* 电子管时代 1950s
* 晶体管时代 1960s
* 中小规模集成电路 1960s、70s
* 大规模、超大规模集成电路 1970s至今

[语言]

* C→C++→Python→Java→Go
* Python居然比Java早
## 为什么要学Linux

* 大学未必学，加分项
* Linux内核源码研读，向大佬学习思维[数据结构、设计模式]
* Linux生态的命令丰富，比Windows事半功倍
    * 比如批量处理文件、分析数据，Windows可能得写文本、找软件
* 目前主流的计算机岗位、技术都基于Linux
## Windows和Mac的恩怨

### [Windows]微软

* 背景：盖茨的妈妈是IBM的高管，在IBM-PC上捆绑式预装微软的DOS系统，其实该系统的推出存在骗局
* 亮点：Excel挤掉了莲花公司，IE击垮了网景，推出了最失败的Vista，收购了github
### [Mac]苹果

* 背景：乔布斯
* 亮点：乔布斯出局又回归，iMac、iPod、iPhone，10年市值超微软，品牌溢价源于品质
### 关联

* 灵感都源于**施乐公司**开发的Alto——有硬盘、显示器、图形界面、以太网等
* 乔布斯先发现Alto，请盖茨帮忙开发Macintosh，盖茨在帮忙同时自己也在开发Windows
* 1984年，Macintosh问世，风靡世界；1985年，Windows 1.0发布，与Macintosh很像，被乔布斯说投了苹果的东西
* 纠葛开始
# 附加知识点

* 云服务器用途：搭建博客、图床，写服务...
* 数据库知识需要会：[MySQL必知必会.pdf](https://github.com/ShawnLeee/the-book/raw/master/MySQL%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A%EF%BC%88%E6%96%87%E5%AD%97%E7%89%88%EF%BC%89.pdf)、[Redis](https://www.wikiwand.com/zh-hans/Redis)键值对存储数据库[[Redis命令参考](http://redisdoc.com/#redis)]
# 思考点

# Tips

* 【推荐书籍】
    * [鸟哥的Linux私房菜](http://linux.vbird.org/linux_basic/)→啰嗦但详细，利于初学者
    * ⭐[apue[UNIX高级编程]](https://pan.baidu.com/s/1BiblranuWMnKM7TIbe6wgQ)提取码：df8j→适合非初学者
* 避免消极的自我暗示：我不行？
* 好学生特质：爱倒腾、拼...
* 路由器的发明来自在异校不能联络的大学老师，参考[最悲催的创始人：虽发明了路由器，却因没资金，最终错失百亿身家](https://kuaibao.qq.com/s/20180915A1M66T00?refer=spider)
* 运维开发、测试开发的目的是干掉运维、测试
    * 运维属于夕阳岗位👈云服务器的流行，有人帮你维护

---


# 课程速记

