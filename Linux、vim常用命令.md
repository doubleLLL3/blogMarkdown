---
title: 'Linux、vim常用命令'
date: 2020-11-19 23:51:00
tags: [计算机]
published: true
hideInList: false
feature: 
isTop: false
---
# Linux

## 常用

|ls|list files|
|:----|:----|
|cd - 或 1|返回**上一次**目录|
|gcc *.c \(-o ...\)|编译C源文件（指定输出的可执行程序名字为...）|
|g++ *.cpp|编译C++源文件，也可以编译C语言|
|./*.out|运行C源文件编译得到的可执行程序|
|mkdir|make directory|
|rm 文件|删除文件|
|mv 旧路径 新路径<br>mv 旧文件名 新文件名|移动文件<br>重命名|
|clear / ctrl + l|清屏|

## 附加

|ld *.o|将目标文件与库链接为可执行文件或库文件<br>有点类似于g++ *.o，但需手动链接库|
|:----|:----|
|g++ -c  *.cpp|生成编译后的对象文件（不链接）|
|g++ -I头文件路径 *.cpp|编译时添加头文件路径到系统库路径下|
|./*.out**>**output|标准输出重定向|
|./*.out**2>**output|错误输出重定向|
|time ./*.out |可显示代码执行时间|
|ctrl + a|光标移到行首|
|ctrl + e|光标移到行尾|
|ctrl + r|搜索之前打过的命令|
|man 指令|查看指令说明|
|touch makefile|创建空白文档 makefile|

## Tips

* 修改zsh控制台显示：用户名hz@后面隐藏主机名字，节省屏幕
    * 在~/.vimc中，找到该行
    * ![图片](https://i.loli.net/2020/11/20/NXIqYu3alOShD4R.png)
    * 主要是删去%m
>PROMPT="%{$fg[red]%}%n%{$reset_color%} :%{$fg[yellow]%}%1~%{$reset_color%}% $ "
* Man手册
# Vim

**默认【普通模式】下**

## 常用

|esc * 2|切换到 普通模式|
|:----|:----|
|i/I|切换到 插入模式/到行首|
|:|切换到 命令行模式|
|v|切换到 visual模式|
|ctrl + v|切换到 visual块模式|
|u|撤销 undo|
|ctrl + r|反撤销 redo|
|d；dd、ndd|剪切、删除（visual模式下；普通模式下某一行、某n行）|
|y；yy、nyy|复制（visual模式下；普通模式下某一行、某n行）|
|p|粘贴（visual模式下；普通模式下）|
|gg|文件头部|
|G|文件尾部|
|h、j、k、l|控制光标左、下、上、右移一格|
|:%s /old/new/g|**全局**替换字符串old为字符串new<br>范围可变，参考[Vim学习笔记 - 常用查找替换命令](https://zihengcat.github.io/2018/01/03/Vim%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0-%E5%B8%B8%E7%94%A8%E6%9F%A5%E6%89%BE%E6%9B%BF%E6%8D%A2%E5%91%BD%E4%BB%A4/#%E6%9B%BF%E6%8D%A2%EF%BC%88Substitute%EF%BC%89)|
|/关键词<br>enter n N|搜索关键词<br>enter定位到关键词 n向后找 N向前找|
|【跨文件拷贝】<br>:sp/:vsp<br>:e *.*<br>yy/dd<br>ctrl + w<br>p|【跨文件拷贝】<br>切分窗口 横向/纵向<br>打开另一文件<br>在某窗口进行拷贝操作<br>切换窗口<br>粘贴|

## 附加

|:set mouse=c|可使用鼠标操作，复制和粘贴对应信息|
|:----|:----|
|Ctrl + ]|找到光标所在位置的标签定义的地方|
|Ctrl + o|退回原来的地方|
|Ctrl + f|下翻一页|
|Ctrl + b|上翻一页|
|f5|编译C、C++|
|f6|代码格式优化|
|f8|调试|
|ddkP 或<br>:m -2|向上移动一行|
|ddp 或<br>:m +1|向下移动一行|
|d n w|删除/剪切n个单词|
|ctrl + o<br>ctrl + i|回到上一视图<br>前往下一视图|

## Tips

* for循环的‘{’后面不要加‘//’注释，否则回车缩进会出错
* 参考[Learn-Vim](https://github.com/iggredible/Learn-Vim)-Github
* vimtutor练习
    * 练习一遍，vim就会用得很6
    * 直接在终端输入**vimtutor**进入





