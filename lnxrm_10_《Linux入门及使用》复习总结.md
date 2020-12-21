---
title: '10 《Linux入门及使用》复习总结'
date: 2020-12-09 18:00:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## [vim基本使用](https://doublelll3.ml/Linux%E3%80%81vim%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/)

* 快速跳转
    * 行首：0 \[^ 有效行首\]
    * 行末：$
    * 文件开始：gg
    * 文件末尾：G
    * 任意行：12G——跳转到12行
* 复制
    * 拷贝
    * 剪切
    * 多行操作
* 删除
    * x：删除一个字符
    * d
        * 2dd：从当前行删除2行
        * d2G：删除第2行到当前行
## [Linux历史](https://doublelll3.ml/lnxrm_1_%E6%8F%AD%E5%BC%80Linux%E6%93%8D%E4%BD%9C%E7%9A%84%E7%A5%9E%E7%A7%98%E9%9D%A2%E7%BA%B1/#%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%AE%80%E4%BB%8B)

* C语言：怎么来的？和系统结合的紧密性？
* Linux本质 [Linux通常指的是Linux内核、Linux操作系统]
* Linux和Windows的不同 [Linux早期对使用者的态度：会的都说好，不会的你随意]
* Unix [MacOS；谷歌提出了云计算后，Unix系统的厂家基本只剩苹果了]
## Shell

* zsh
    * [相关文件](https://doublelll3.ml/lnxrm_5_%E5%9F%BA%E6%9C%AC%E7%B3%BB%E7%BB%9F/#shell)
    * 环境变量的概念：存在shell进程中
    * 执行命令的本质：大部分是执行文件，开一个子进程 [保证命令间不干扰]，运行完子进程结束，返回结果
* man
    * 简约版：tldr
    * 英语语感
    * 检索：前后移动——N/n
    * C语言底层库函数
    * 搜索引擎 [Google 英语搜索]：提升自己的信息处理能力
* 命令怎么查找
    * tab [zsh提供]
    * man -k [-f：基于明确的单词搜索]
    * apt search [apt-cache search]
## Linux具体使用

* 文件及目录
    * 远程拷贝
        * scp [基于ssh->基于tcp]
        * sshfs [基于ssh、远程文件系统]：可以将远程服务器的目录挂载到本地
            * 可应用于嵌入式开发过程
    * 文件查阅
        * cat
        * head
        * tail
            * -f：可查看动态更新的文件，一直读文件末尾，用来监控日志 [会阻塞]
        * more
        * less [比more更友好]
            * 可回看
            * 检索时高亮显示
    * 移动、复制
        * cp、mv
        * ln
            * 硬连接
            * 软连接：删除软连接时一定要小心，名字后别多加/ [会进入到目录]
                * [PS] Python结束对2的支持；Python的版本切换只需更改软连接即可
        * rm：可封装成mv + 定时清理
    * [文件类型及权限](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E6%96%87%E4%BB%B6%E7%B1%BB%E5%9E%8B)
        * 7种
            * 普通文件
            * 目录：大小一般为4K，够用
            * 字符：基于网络、串口-->键盘、鼠标
            * 块：block，存储设备
            * 管道 [更多的情况是父进程生两个孩子，中间建立管道]
            * 链接
            * socket
        * 权限
            * rwx
                * x对于目录表示可进入
            * 用户分组：u、g、o
            * 命令
                * chmod、chgrp、chown
                * usermod：针对用户
            * 特殊权限
                * set uid：拥有尚方宝剑的人就有权利
                * set gid：到了某个地方就有特定的身份
                * sbit：黏着位，只有文件所属者有删除权限
            * 隐藏属性
    * [文件时间](https://doublelll3.ml/lnxrm_8_%E6%96%87%E4%BB%B6%E4%B8%8E%E7%9B%AE%E5%BD%95%E3%80%81AWK/#%E4%BF%AE%E6%94%B9%E6%96%87%E4%BB%B6%E6%97%B6%E9%97%B4%E4%B8%8E%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6)
        * atime[访问]：更新过于频繁不好，可以通过设置隐藏属性不更新
        * ctime[修改权限]
        * mtime[修改内容]：ls默认显示的
        * touch
        * 站得越高，越需要考虑磁盘的寿命 [损耗、电费]
    * [文件定位](https://doublelll3.ml/lnxrm_8_%E6%96%87%E4%BB%B6%E4%B8%8E%E7%9B%AE%E5%BD%95%E3%80%81AWK/#%E5%91%BD%E4%BB%A4%E4%B8%8E%E6%96%87%E4%BB%B6%E7%9A%84%E6%9F%A5%E8%AF%A2)
        * 特殊文件的定位
            * which
            * whereis
        * 普通文件
            * locate：基于数据库db，实时需要updatedb [使用场景少]
            * find
* [数据处理](https://doublelll3.ml/lnxrm_9_%E6%95%B0%E6%8D%AE%E6%8F%90%E5%8F%96%E3%80%81%E8%BD%AF%E7%A1%AC%E8%BF%9E%E6%8E%A5%E3%80%81%E7%BA%BF%E6%80%A7%E7%AD%9B%E3%80%81SED/#%E6%95%B0%E6%8D%AE%E6%8F%90%E5%8F%96%E6%93%8D%E4%BD%9C)[适于将来工作]
    * cut、tr、sort、uniq、grep、head、tail
    * 重定向：>、>>、<
    * read：读入数据 [注意输入数据的格式]
* [进程管理](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E8%BF%9B%E7%A8%8B%E7%9B%B8%E5%85%B3)
    * ctrl + z
    * fg
    * bg
    * jobs
    * crontab -e/-l [应该会，会看任务的格式]* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/bTXIrrj.png" alt="图片" style="zoom: 67%;" />
* [系统信息的获取](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E8%8E%B7%E5%8F%96%E7%B3%BB%E7%BB%9F%E4%BF%A1%E6%81%AF)[试试即可]
    * uname、top、free、dstat、htop、nmon、uptime
## [Shell编程](https://doublelll3.ml/lnxrm_7_Shell%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80/)

* 变量
    * 命令替换符：``
    * 特殊变量：\$0、\$#、\$?、\$@、\$*
    * ❗ =两边没有空格
* 输入输出
    * read
    * echo
    * printf
* 分支
    * test表达式
        * man test
        * 整数判断：整数计算$[] （还有expr、let，能记住一个即可）
        * 字符串：== 🆒
    * if：[[  ]] 🆒
    * case
* 循环
    * for
        * for i in \`seq 1 100\` --> seq需被系统不停地调用
        * for ((i=1; i<=100; i++)) 🆒效率更高
    * while [一般用 while 1]
    * until [一般不用]
* 函数 [简单程序一般不需要]
* [数组](https://doublelll3.ml/lnxrm_7_Shell%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80/#%E6%95%B0%E7%BB%84)⭐
    * 数组的初始化 [可有可无；没初始化就为空；初始化耗时]
    * unset
    * 遍历
    * $变量

⭐\[PS\][素数筛](https://doublelll3.ml/lnxrm_7_Shell%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80/#%E7%B4%A0%E6%95%B0%E7%AD%9B)、[线性筛](https://doublelll3.ml/lnxrm_9_%E6%95%B0%E6%8D%AE%E6%8F%90%E5%8F%96%E3%80%81%E8%BD%AF%E7%A1%AC%E8%BF%9E%E6%8E%A5%E3%80%81%E7%BA%BF%E6%80%A7%E7%AD%9B%E3%80%81SED/#%E7%BA%BF%E6%80%A7%E7%AD%9B)必考！

# Tips

* 考试包含全英文题
# 附：思维导图

【宿船长出品】

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/yxqh5X1.png" alt="图片" style="zoom: 67%;" />

