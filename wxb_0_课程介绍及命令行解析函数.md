---
title: '0 课程介绍及命令行解析函数'
date: 2020-12-29 18:00:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911406512-HDhO1eLXOwqHZUGh.png" alt="图片" style="zoom:80%;" />

[如何实现命令选项？可以想到主函数中的输入参数argc、argv]

# 课程内容

## getopt函数

【命令行解析函数】

* <unistd.h> → Unix Standard
* ⭐函数原型
    * int getopt(int argc, char * const argv[], const char *optstring);
    * 猜测
        * 返回值 int
            * ① 0：成功；-1：失败
            * ② ≥0：成功；<0：失败
        * 形参 argc、argv：像主函数的形参
        * 形参 optstring：选项字符串
    * 查看man手册：man 3 getopt
        * 返回值是字符 [选项] 或-1
            * 返回时会更新外部变量optind和静态变量nextchar
            * 如果没有选项了，返回-1，optind移到argv的第一个元素位置 [不是选项]
        * argc、argv
            * 由主函数参数直接传递而来
        * optstring
            * 含义：所有合法的可选字符组成的字符串
            * 单个字符，表示选项
            * 单个字符后面如果跟着冒号
                * 一个冒号，表示该选项后必须跟一个参数值
                    * 参数紧跟在选项后或者以空格隔开
                    * [类似命令里[选项参数](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E5%91%BD%E4%BB%A4%E6%A0%BC%E5%BC%8F)的存在]
                * 两个冒号，意味着选项后的参数是可选的
                    * 如果有参数，必须跟在选项后，而不能用空格
                * 参数的指针赋给optarg [对于两个冒号，默认为0←null]
            * 举例
                * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911404308-JhyyQmBIWiwB8P75.png)
                * x、y、z代表3个选项
        * 重复调用getopt()可以读入多个选项，如-al -h
        * 一些全局变量
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911402335-akTACDlhmfYR0lpU.png" alt="图片" style="zoom: 50%;" />
            * optarg：当有冒号时，可以提取选项参数
            * optind
                * 含义：argv数组中下一个元素 [字符] 的索引
                * 调用者重设其为1时，可以重新扫描argv
                * [PS] 外部变量；像一个指针
            * 可以直接使用 [都不需要声明]，因为在【引用的头文件】中已经定义了这些变量
                * 外部变量的概念
# 代码演示

## getopt

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911400758-hmfZYX96rs757qrg.png" alt="图片" style="zoom:67%;" />
* 学会使用flag、switch
* 【冒号的含义】必选/可选 选项参数
* 注意getopt相关的全局变量，可直接使用
* exit()可使用非0数值表示非正常退出
* 程序结果
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911399913-Z0pqT0Rd6bfYMicy.png" alt="图片" style="zoom:67%;" />
    * 供参考
# 附加知识点

# 思考点

# Tips

* ⭐**小作业**：用C语言实现ls -al命令
    * 学会使用流程图理清思路，推荐[ProcessOn](https://www.processon.com/)——在线绘制流程图网页
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1609911399913-IvZXSciC1Uz4N7HH.png" alt="图片" style="zoom: 67%;" />
        * 来自宿船的简易流程图
* 初学命令时不用过多地关注变种
* 在vim里，输入:Tlist可以查看源代码的结构化视图
* ctags常用操作：ctrl + ] 跳转到定义，ctrl + o 返回

---


