---
title: '回”家“问候 -- SSH连接Linux后的友好显示'
date: 2020-12-04 09:12:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: false
feature: 
isTop: false
---
**实验环境**：Ubuntu18.04远程服务器 + WSL2本机

# 功能需求

* <img src="https://i.loli.net/2020/12/04/JeWSBanmuyzKMl9.png" alt="图片" style="zoom:67%;" />

# 最终成果

在实现上述功能的基础上，还添加了【实时股票数据、上次登录时间、本月共使用时间】，有一定的容错性【Shell判断、API调用失败返回默认值】，还有一些可尝试功能，见文末【历史上的今天、拒绝的拜访者、上次登录输入的命令统计、在终端显示图片】

![图片](https://i.loli.net/2020/12/04/rN9DbT32WCfOnS6.png)

* em..今天似乎不是好日子，股市绿了:)

---


——>下面时间扭转<——

# 实现过程

【首先瞅瞅连接主机的默认消息】

![图片](https://i.loli.net/2020/12/04/eYxaKRuHzGsD2Th.png)

* 这些消息我都不要，一切重头，做一个完全自主[到处拼凑]的消息提醒
## 原开机信息去除

>如何去除上面这些信息呢？
* 经高人[宿船长]指点，“到/etc/update-motd.d里去找找”，vim update-motd.d显示如下：
    * ![图片](https://i.loli.net/2020/12/04/EoI6tc8yKCT53JV.png)
    * 原本以为是个文件...进去点开每个文件可以对应到上面的登录信息
* 这里motd的意思是message of the day——每日消息
    * 它可以是每次登录都更新一次的；也可以是事先存好不变的
    * 可参考man 5 update-motd，这里参考[MOTD是什么？](http://cnblogs.com/SsoZhNO-1/p/12371435.html)-cnblogs
    * 里面提到：是由一个叫pam的服务下的login模块管理着MOTD的输出，sudo vim /etc/pam.d/login，注释掉与motd相关的功能后，**但是**并不生效
* 原来/etc/pam.d/login是用来控制本机登录的，而我使用SSH登录，有另一个配置文件/etc/pam.d/sshd
    * 参考[Disable everything in update-motd.d dir in ubuntu server](https://superuser.com/questions/375714/disable-everything-in-update-motd-d-dir-in-ubuntu-server)-StackExchange
    * ![图片](https://i.loli.net/2020/12/04/GnT8odmeCqiVuBa.png)
    * 我要禁用通过SSH连接后的登陆提示消息，所以sudo vim /etc/pam.d/sshd进入
    * 对motd相关功能进行注释
        * ![图片](https://i.loli.net/2020/12/04/C9ZuMSGQzV21Idm.png)
        * 我要禁用的是update-motd.d里的显示消息，属于动态信息，所以注释第33行即可
        * 而静态消息的来源/etc/motd在主机上不存在，可以不管

【重连主机，清爽许多】

![图片](https://i.loli.net/2020/12/04/SW5L4cq29ZKluFj.png)

* 但还有一个Last login信息
    * 它属于sshd所控制，找到sshd的配置文件
    * sudo vim /etc/ssh/sshd_config，将PrintLastLog设置为no
    * 再重启sshd服务使其生效，sudo service sshd restart [似曾相识]

【至此，回到了解放前】

![图片](https://i.loli.net/2020/12/04/DVTohGIkCW6mMt9.png)


---


## 前期准备

### 找到开机运行的文件

【使用zsh开机配置文件】

Shell：zsh

* 通过man zsh搜索FILES，查看zsh的开机配置文件，有很多
* ![图片](https://i.loli.net/2020/12/04/hi9SfpGjxDHqY3l.png)
* 根据语境，使用.zlogin配置
* 在家目录下创建.zlogin，vim ~/.zlogin
### 最近登录次数[本月]

【本月登录：40次】

![图片](https://i.loli.net/2020/12/04/SP7L5mx9XtnCGJe.png)

【通过last获取】

* 当前用户
    * 通过截取who am i 的第一个元素：who am i | cut -d " " -f 1
    * 通过获取USER环境变量：env | grep USER | cut -d "=" -f 2
* last获取登录信息→查找当前用户的登录信息并计数
```shell
last | grep -c `env | grep  USER | cut -d "=" -f 2`
```
[PS] 显示更多的信息需找到：

* /var/log/wtmp.x，x ∈ [0-9]
* 参考[How to read older login info using the “last” command?](https://askubuntu.com/questions/443206/how-to-read-older-login-info-using-the-last-command)——StackExchange
### 上次停留时间[本月]

【上次停留：01小时03分】

![图片](https://i.loli.net/2020/12/04/WZ9yFo57w6LYjAa.png)

【通过last获取】

* last获取登录信息→找到该用户的登录信息→匹配“()”式样的连接时间的行→获取最近一次的→按照“ ( ”、“ ) ”切割得到上次停留时间，
    * 时间格式为小时: 分钟
    * 参考[AWK - 正则表达式](https://www.twle.cn/c/yufei/awk/awk-basic-regular-expressions.html)——简单教程
```shell
last | grep `env | grep USER | cut -d "=" -f 2` | awk '/\(*\)/' | head -1 | cut -d "(" -f 2 | cut -d ")" -f 1
```
### 寻找名人名言接口

【名人名言显示】

![图片](https://i.loli.net/2020/12/04/X2xerPtOJZAwsYQ.png)

【来源：[名人名言](http://free-api.com/doc/372)——Free API】

* 每天最多申请100次
* 通过curl请求：curl https://v1.alapi.cn/api/mingyan
* 返回格式：json
* 可选参数：typeid [1~45]
* 随机获得的json数据如下：

![图片](https://i.loli.net/2020/12/04/IPENwgS2lzKXHmW.png)

【使用[jq](https://stedolan.github.io/jq/)处理json数据】

* 使用sudo apt install jq安装
* 使用如下命令
```shell
curl -s https://v1.alapi.cn/api/mingyan | jq '.data | [.content, .author]' | jq 'join("————")'
```
* 参考[jq tutorial](https://stedolan.github.io/jq/tutorial/)（简单使用）、[jq manual](https://stedolan.github.io/jq/manual/)（细节操作）
* 另参考[使用 Shell 脚本来处理 JSON](https://www.tomczhen.com/2017/10/15/parsing-json-with-shell-script/)——博客
    * 虽然可以使用awk、sed命令去针对不同的json数据做处理，但这里使用强大的jq有更强的鲁棒性
### 寻找当地当日天气预报接口

【天气显示】

![图片](https://i.loli.net/2020/12/04/oNIk6BdWSunVt5v.png)

【来源：[wttr.in](https://github.com/chubin/wttr.in)——Github】

* 可一行输出，更简洁
* 自定义参数如下：

![图片](https://i.loli.net/2020/12/04/n2cEl1q9Q3upfyh.png)

* 主要显示位置、天气图标、体感温度以及晚上的月亮形状，调用如下命令
```shell
curl wttr.in/\?format="%l:+%c+%t+feels+like+%f,+moon+tonight:+%m\n"
```
### 设置温馨问候

【问候，随机颜色】

![图片](https://i.loli.net/2020/12/04/u4T9QPAx3tjykMU.png)

【使用[figlet](https://github.com/cmatsuoka/figlet)、[lolcat](https://github.com/busyloop/lolcat)生成艺术字】

* 可通过sudo apt install figlet安装
    * [扩充字体库][figlet-fonts](https://github.com/xero/figlet-fonts)——Github，git clone后，把所有文件放到/usr/share/figlet/里
* 通过showfigfonts "Hey Double"可以查看关于Hey Double所有字体演示
* figlet的可选项很多，如下：
* ![图片](https://i.loli.net/2020/12/04/iHb5xXlcszRU9gn.png)
* 自行搭配后选择自己喜欢的样式，我的搭配：figlet -t -r Hey Double -f miniwi
* 再使用 lolcat 给问候上色：通过sudo apt install lolcat即可安装
* 使用如下命令
```shell
figlet Hey Double -f miniwi | lolcat
figlet -t -r You\'re back -f miniwi | lolcat
```

---


## 脚本制作[含颜色优化]⭐

### **~/.login.sh**

![图片](https://i.loli.net/2020/12/04/EGpQe7XTfxvN21y.png)

![图片](https://i.loli.net/2020/12/04/dhBEVL7aGjKTYgX.png)

![图片](https://i.loli.net/2020/12/04/xf92FWdI7hJnb41.png)

* 【关键】在于jq、awk、sed的使用
* 如果curl返回不是预期的，做一些保护机制
* 对于一些边界，以及数组、变量的操作，日后再多加琢磨
* ⭐注意zsh、bash的兼容性
    * zsh下：数组索引从1开始，特殊符号一定要转义❗
* [PS] 
    * 额外添加的功能【实时股票数据、上次登录时间、本月共使用时间】见文末——附加部分
    * 股票数据的appkey和sign需要去[官网](https://www.nowapi.com/api/finance.globalindex)生成自己的
### **.zlogin**

![图片](https://i.loli.net/2020/12/04/oUwc6yql8t4jMVR.png)

* ⭐不使用source调用脚本，而是根据Shell类型使用zsh/bash[默认bash]，因为
    * 这个开机脚本只是用来显示，不需要添加环境变量
    * 使用zsh/bash会开一个子Shell运行脚本，而使用source是在当前Shell运行，脚本里添加的变量会在环境中生效 [可通过set查看所有本地定义的环境变量]
### 【探讨】source和bash调用脚本的区别

* bash和source脚本的结果不一致，前面测试都是用的bash，而开机脚本是使用source执行的，会调用当前Shell——zsh执行
* 开机显示如下
* ![图片](https://i.loli.net/2020/12/04/sB4DyHxePlwo7kE.png)
* 使用set -x打开脚本调试，可以看到完整过程
    * ![图片](https://i.loli.net/2020/12/04/lLaucWdoprvtYnB.png)
    * 左边是代码，右边是调试结果
    * 可以看到index变量变成空的了！
* 【①】原来就是zsh和bash中，传说中的【**数组起始索引**】不一致的问题
* 于是考虑对运行脚本的shell做一个判断
    * 使用${SHELL}判断并不有效，在zsh里不管用source还是bash都指向/usr/bin/zsh
    * 但有另一种检测方法
        * ![图片](https://i.loli.net/2020/12/04/Gt3kscHd8DJjRpa.png)
        * 参考[How to get shell to self-detect using zsh or bash](https://stackoverflow.com/questions/9910966/how-to-get-shell-to-self-detect-using-zsh-or-bash)——Stackoverflow
    * 【或】可借鉴下文方法，调用数组元素的奇门绝技？未尝试，上述方法可行🆗
        * ![图片](https://i.loli.net/2020/12/04/tceWMT5HKVydoZv.png)
        * 参考[Behavior of Arrays in bash scripting and zsh shell (Start Index 0 or 1?)](https://stackoverflow.com/questions/50427449/behavior-of-arrays-in-bash-scripting-and-zsh-shell-start-index-0-or-1/50433774)——Stackoverflow
* 【②】数组下标问题解决了，还要注意zsh的其它兼容性问题
    * ❗ 特殊符号，这里jq后面的[]需要使用转义符\

【参考】

* [Shell命令替换：将命令的输出结果赋值给变量](http://c.biancheng.net/view/1164.html)——C语言中文网
    * $() 支持嵌套，反引号不行
    * $() 仅在 Bash 中有效，而反引号可在多种 Shell 中使用
* [Shell 中实现字符串切割的几种方法](https://blog.csdn.net/u010003835/article/details/80750003)——CSDN
    * ⭐arr=(${parameter//pattern/string})
    * echo args |   tr "oldSpilt" "newSpilt"
* [awk入门教程](https://www.ruanyifeng.com/blog/2018/11/awk.html)——阮一峰
* [awk格式化打印](https://www.yiibai.com/awk/awk_pretty_printing.html)——易百教程
* [Shell source命令：使环境变量配置文件强制生效](http://c.biancheng.net/view/977.html)——C语言中文网

---


# 附加

## [全球股票指数](https://www.nowapi.com/api/finance.globalindex)

```shell
curl http://api.k780.com/\?app=finance.globalindex\&inxids=1010,1011,1013\&appkey=你的key\&sign=你的sign | jq .
```
## 上一次登录时间

* last
```shell
last | grep `env | grep USER | cut -d "=" -f 2` | awk '/\(*\)/' | head -1 | awk '{printf "%s %s %s —— %s", $4, $5, $6, $7}'
```
* 有待改进，切割方式较死板
## 本月共使用的时间

```shell
ac | awk '{print int($NF)}'
```
* ac命令：显示用户的连接时间数据
    * ac 该用户本月共使用的时间
    * ac -d 该用户本月每天的连接时间
    * 参考[ac命令详解](https://commandnotfound.cn/linux/1/238/ac-%E5%91%BD%E4%BB%A4)——commandnotfound
## ——可尝试——

### [历史上的今天](https://www.free-api.com/doc/72)

```shell
curl http://api.juheapi.com/japi/toh\?key=你的key\&v=1.0\&month=11\&day=1
```
* 统计有多少条记录
```shell
curl -s http://api.juheapi.com/japi/toh\?key=你的key\&v=1.0\&month=1\&day=1 | jq '.result | length'
```
* 随机输出其中一条
    * 生成0~N的随机数
```shell
$(( ${RANDOM} % ${N} ))
```
### 拒绝了多少位拜访者

* 从上次登录到现在，有多少次失败登录记录
```shell
sudo lastb
```
* 需要sudo权限，只想到在脚本里放密码，不安全
### 上次登录输入的命令统计

【通过history统计】

* 输入了多少条命令
* 哪个命令是你的最爱
* 经常输错的是什么命令

目测比较耗时


---


# 参考资料

* 在终端显示图片，可参考[termpix](https://github.com/hopey-dishwasher/termpix)——Github
*  [curl 的用法指南](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)——阮一峰
* [Free API 接口大全](https://www.free-api.com/)
    * [历史上的今天](https://www.free-api.com/doc/72)
    * [微信精选](https://www.free-api.com/doc/76)
    * [笑话大全](https://www.free-api.com/doc/77)
    * [爱飞天气](https://www.free-api.com/doc/105)
    * [全球IP地址查询](https://www.free-api.com/doc/107)
    * [空气质量指数](https://www.free-api.com/doc/179)
* [NowAPI](https://www.nowapi.com/)
    * [全球股票指数](https://www.nowapi.com/api/finance.globalindex)
* [在 Linux 字符界面中获取天气预报](https://zhuanlan.zhihu.com/p/33441651)——知乎
* [Is there a reason why the first element of a Zsh array is indexed by 1 instead of 0?](https://unix.stackexchange.com/questions/252368/is-there-a-reason-why-the-first-element-of-a-zsh-array-is-indexed-by-1-instead-o)——StackExchange
    * Bash里的数组下标从0开始，而zsh从1开始
* [在 Linux 中配置SSH登录导语](https://linux.cn/article-9422-1.html) [静态信息：Banner、motd]——博客
