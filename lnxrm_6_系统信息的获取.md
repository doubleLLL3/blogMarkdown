---
title: '6 系统信息的获取'
date: 2020-11-29 18:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

## uptime 系统运行时长和平均负载

* <img src="https://i.loli.net/2020/12/09/AMqnjQD3imcrZH6.png" alt="图片" style="zoom:67%;" />

* 用来监测系统开机多久了、有几个用户登陆着、运行状态(负载)
* 学会用--help查看命令帮助

* <img src="https://i.loli.net/2020/12/09/8wKNtuz9L7JOZro.png" alt="图片" style="zoom: 67%;" />

## w 用户列表及运行的任务

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/ijgPefx.png" alt="图片" style="zoom: 50%;" />

* <img src="https://i.loli.net/2020/12/09/Ic13dBaMvzxOr5V.png" alt="图片" style="zoom: 50%;" />

* 当系统很卡时，可以看看当前进程正在执行的命令
## who 显示当前登录用户信息

* <img src="https://i.loli.net/2020/12/09/LowVWIDREJUmCpH.png" alt="图片" style="zoom:67%;" />

* 有点像精简的w
* 当--help信息过多时，学会使用tldr
* -q 查看所有登陆用户及数量
* -H 显示头
## whoami、who am i 当前有效/真实的用户名

* <img src="https://i.loli.net/2020/12/09/YExqImKayv56ohU.png" alt="图片" style="zoom:67%;" />

* sudo -i 用超级用户权限启动默认Shell
* who i am = who am i
* who am i 其实属于who命令，am i 是参数

* <img src="https://i.loli.net/2020/12/09/7vLdX3OeTnYSapZ.png" alt="图片" style="zoom:67%;" />

## last 用户最近登录信息

应用：查出最近登录最频繁的三个用户

* <img src="https://i.loli.net/2020/12/09/baodHKE7PZrOFux.png" alt="图片" style="zoom: 50%;" />

* 清洗空行【"^$"】、按空格切割再取第1列、按字典序[即ASCII码序]排序、计数、按数字排倒序
* cut -c 10-15：按字符切，取某一列对应第10-15个字符

【延伸】词频统计（a.log为man手册的ls、man、stdio、scanf内容）

* <img src="https://i.loli.net/2020/12/09/l7DibyoALqhKQkW.png" alt="图片" style="zoom: 50%;" />

* tr 字符替换命令
    * -c 反向替换
    * -s 压缩连续的重复字符
## uname 系统信息

* uname -a 很完整的系统信息 [机器一上手先用这条命令]
## date 显示或设置系统时间与日期

* <img src="https://i.loli.net/2020/12/09/FNzOk7JEdTRY69t.png" alt="图片" style="zoom:67%;" />

* 可以自定义字符串格式，注意大小写自行尝试即可
* [PS] NTP——网络时间协议，通过时区同步时间
    * <img src="https://i.loli.net/2020/12/09/GalbuZp8q91rXQV.png" alt="图片" style="zoom: 50%;" />
    * 来自[维基百科](https://www.wikiwand.com/zh-hans/%E7%B6%B2%E8%B7%AF%E6%99%82%E9%96%93%E5%8D%94%E5%AE%9A)，分布式结构类似DNS，但它是单向联系的
    * 应用：银行系统、网购
    * 时间服务器配置见/etc/ntp.conf
    * 时区可查看/usr/share/zoneinfo
        * <img src="https://i.loli.net/2020/12/09/O5mcnvIZ7o14BdC.png" alt="图片" style="zoom:80%;" />
        * 修改时区可通过ln -sf [软连接强制覆盖]命令，将/etc/localtime连接到时区
        * 举例：sudo ln -sf /usr/share/zoneinfo/Asia/城市 /etc/localtime
# 附加知识点

* cal 显示日历
* echo -e 可以让反斜杠\生效
    * 如echo -e "\033[32m123\033[0m456" 输出颜色，这类似printf
* $[]只做整数计算
# 思考点

# Tips

* 缩写可能代表
    * -h：help、human、header(w -h)
    * -p：pretty
* shell脚本里空格是敏感的
* 在Shell里定义的变量是在进程里的，退出Shell [或断开SSH连接] 变量就消失了
* wget可以下载文档数据

---


# 课程速记

