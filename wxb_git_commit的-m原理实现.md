---
title: 'git commit的-m原理实现'
date: 2021-01-17 18:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: false
feature: 
isTop: false
---
# 功能要求

[类似git commit -m "msg"的功能]

* 使用-m选项时直接打印消息，未使用-m选项时自动打开vim供输入消息
* 详细说明
    * ① 当含有选项和选项参数-m "first commit"时
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/0xM7G6wlHc4iKOMK.png" alt="图片" style="zoom:67%;" />
        * 直接打印相关消息
    * ② 当没有-m选项时
        * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114221.png)
        * 自动打开vim，用户在里面输入"second commit"，并保存退出后，再打印相关消息
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114230.png" alt="图片" style="zoom:67%;" />
        * 如果在vim中没有输入有效消息 [注释不属于有效消息]
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114252.png" alt="图片" style="zoom:67%;" />
        * 该操作会失败，并友好提示用户
* [PS]
    * 可能需要3个进程：父进程、vim进程、删除vim产生的文件的进程
    * 需要判断vim里输入的消息是否有效
    * 子进程跑，父进程等，读数据，打印出来 [cat]，rm
# 最终效果

* 输入./git_commit -m "first commit"
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114322.png" alt="图片" style="zoom:67%;" />
    * 直接输出消息
* 输入./git_commit
    * 先弹出vim，可输入任意消息
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114334.png)
    * ① 若输入包含注释的多行消息
        * ![](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114354.png)
        * 最终打印结果如下
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114406.png" style="zoom:67%;" />
        * 多行整合为一行 [同git设计]，并屏蔽注释
    * ② 若直接:q退出vim
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114443.png" alt="图片" style="zoom:67%;" />
        * 提示无法打开消息文件
    * ③ 若输入全是注释
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114451.png" alt="图片" style="zoom:67%;" />
        * 提示消息为空

[PS]

* vim打开文件时，是否可以指定其TYPE=GITCOMMIT？目前TYPE为空
    * 👉 vim打开的文件名改为COMMIT_EDITMSG即可，这样消息代码就有些配色了
# 实现过程

## 思路流程图

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114513.png" alt="图片" style="zoom: 50%;" />

【进程分析】

* 因为后面还需要创建**rm**的进程，所以不能将父进程直接替换为**vim**进程
    * 否则退出vim后，程序就结束了
* 父进程需要fork一个子进程，再exec族将该进程替换为**vim**进程
* 父进程再fork一个子进程，并替换为**rm**进程
    * 直接将父进程直接替换为**rm**进程，也可行
    * 但fork一个新进程，可以让父进程监控删除状态，并有可能进行更多的操作
## 获取命令行参数

捕捉-m选项，使用该选项必须带参数

```c++
#include "head.h"
int main(int argc, char **argv) {
    if (argc == 1) {
        // 1.无选项：需要vim输入msg
        printf("no msg, need vim.\n");
    }
    int opt;
    while ((opt = getopt(argc, argv, "m:")) != -1) {
        switch (opt) {
            case 'm': {
                // 2.有-m及选项参数：打印参数
                printf("msg: %s\n", optarg);
            } break;
            default: {
                // 3.选项不合法：友好提示
                fprintf(stderr, "Usage: %s [-m msg]\n", argv[0]);
                exit(1);
            }
        }
    }
    return 0;
}
```
* 只有三种情况：① 无选项；② 有-m选项与选项参数；③ 选项不合法
* 头文件head.h见末尾
* 使用测试脚本test.sh [见末尾] 测试，效果如下：
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114533.png" alt="图片" style="zoom:67%;" />
    * 展示了三种情况下的输出
## 输入消息

利用vim供输入消息

* 父进程使用fork创建子进程，再利用execlp将其替换为vim进程
* 父进程使用wait监控子进程状态，并避免产生僵尸进程
```c++
// 输入消息
void input_msg() {
    pid_t pid;
    int status;
    // 父进程复制一个子进程
    if ((pid = fork()) < 0) {
        perror("fork()");
        exit(1);
    }
    if (pid == 0) {
        // 将子进程替换为vim进程
        execlp("vim", "vim", "COMMIT_MSG", NULL);
    } else {
        wait(&status);  // 监控子进程状态
        // 父进程提示vim出错
        if (WEXITSTATUS(status)) printf("vim error!\n");
        else printf("input msg completed!\n");
    }
    return ;
}
```
* 命令无选项时，用vim进程打开COMMIT_MSG文件
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114552.png)
    * 用户可输入消息
* 退出vim后，vim进程终止，父进程可感知
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/76aV94dOxwPhW5Vh.png" alt="图片" style="zoom:67%;" />
    * 父进程提示"input msg completed!"
## 读取消息

父进程尝试读取消息文件

* 【友好提示】消息文件可能无法打开 [如文件不存在]，也可能不包含有效消息 [全是注释]
* 【分行解析】判断行注释的存在；如果输入了多行有效消息，打印结果时整合到一行，每行消息用空格隔开 [同git的设计]
```c++
#define MAX_LENGTH 512
// 读取消息
void read_msg() {
    int fd, flag = 0;                    // flag：是否读取到了有效消息
    char buff[MAX_LENGTH] = {0};         // 最多读取MAX_LENGTH字节消息
    ssize_t nread;
    // 如果无法打开消息文件，友好提示
    if ((fd = open("COMMIT_MSG", O_RDONLY)) < 0) {
        printf("aborting commit due to msg file can't open.\n");
        exit(1);
    }
    // 读取消息文件
    if ((nread = read(fd, buff, sizeof(buff) - 1)) > 0) {
        char *line;
        // 分行判断消息是否有效
        line = strtok(buff, "\n");
        while (line != NULL) {
            // 如果不是无效消息 [不是注释]
            if (line[0] != '#') {
                flag || printf("msg:");  // 第一次打印有效消息时打印消息前缀："msg:"
                flag = 1;
                printf(" %s", line);
            }
            line = strtok(NULL, "\n");   // 不断调用直到遇到buff的'\0'
        }
        flag && printf("\n");
    }
    // 如果没有读到有效消息，友好提示
    if (!flag) {
        printf("aborting commit due to empty commit msg.\n");
    }
    close(fd);
    return ;
}
```
* 特判flag输出消息前缀："msg:"
* 最多读取消息文件的前512个字节
* ⭐字符串分割函数strtok——[cplusplus](http://www.cplusplus.com/reference/cstring/strtok/?kw=strtok)
    * char\* strtok(char\* str, const char\* delimiters)
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114631.png" alt="图片" style="zoom:67%;" />
    * 需要不断调用，从第二次起传入的str替换为NULL，每次调用返回一个被分割的字符串，返回NULL表示到达str末尾
* 效果如下：
    * 命令无选项时，在vim中输入下列消息
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114643.png)
    * 读取成功后，整合到一行打印
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/20210120114654.png" alt="图片" style="zoom:67%;" />
    * 注释会被忽略
## 删除消息文件

为了让父进程有可能做更多事，这里fork了一个子进程，并替换为rm进程

* 父进程可监控rm进程发挥状态，虽然rm进程也会报失败信息
```c++
// 删除消息文件
void remove_file() {
    pid_t pid;
    int status;
    if ((pid = fork()) < 0) {
        perror("fork");
        exit(1);
    }
    if (pid == 0) {
        execlp("rm", "rm", "COMMIT_MSG", NULL);
    } else {
        wait(&status);
        // 父进程提示rm失败 [rm进程也会有提示]
        if (WEXITSTATUS(status)) printf("remove msg file error!\n");
    }
    return ;
}
```
* 使用完消息文件后，不再留下COMMIT_MSG文件
# 完整代码

## git_commit.c

```c++
#include "head.h"
#define MAX_LENGTH 512
// 输入消息
void input_msg() {
    pid_t pid;
    int status;
    // 父进程复制一个子进程
    if ((pid = fork()) < 0) {
        perror("fork()");
        exit(1);
    }
    if (pid == 0) {
        // 将子进程替换为vim进程
        execlp("vim", "vim", "COMMIT_MSG", NULL);
    } else {
        wait(&status);  // 监控子进程状态
        // 父进程提示vim出错
        if (WEXITSTATUS(status)) printf("vim error!\n");
    }
    return ;
}
// 读取消息
void read_msg() {
    int fd, flag = 0;                    // flag：是否读取到了有效消息
    char buff[MAX_LENGTH] = {0};         // 最多读取MAX_LENGTH字节消息
    ssize_t nread;
    // 如果无法打开消息文件，友好提示
    if ((fd = open("COMMIT_MSG", O_RDONLY)) < 0) {
        printf("aborting commit due to msg file can't open.\n");
        exit(1);
    }
    // 读取消息文件
    if ((nread = read(fd, buff, sizeof(buff) - 1)) > 0) {
        char *line;
        // 分行判断消息是否有效
        line = strtok(buff, "\n");
        while (line != NULL) {
            // 如果不是无效消息 [不是注释]
            if (line[0] != '#') {
                flag || printf("msg:");  // 第一次打印有效消息时打印消息前缀："msg:"
                flag = 1;
                printf(" %s", line);
            }
            line = strtok(NULL, "\n");   // 不断调用直到遇到buff的'\0'
        }
        flag && printf("\n");
    }
    // 如果没有读到有效消息，友好提示
    if (!flag) {
        printf("aborting commit due to empty commit msg.\n");
    }
    close(fd);
    return ;
}
// 删除消息文件
void remove_file() {
    pid_t pid;
    int status;
    if ((pid = fork()) < 0) {
        perror("fork");
        exit(1);
    }
    if (pid == 0) {
        execlp("rm", "rm", "COMMIT_MSG", NULL);
    } else {
        wait(&status);
        // 父进程提示rm失败 [rm进程也会有提示]
        if (WEXITSTATUS(status)) printf("remove msg file error!\n");
    }
    return ;
}
int main(int argc, char **argv) {
    if (argc == 1) {
        // 1.无选项：需要vim输入msg
        input_msg();
        read_msg();
        remove_file();  // 如果可以到这步，消息文件已产生
    }
    int opt;
    while ((opt = getopt(argc, argv, "m:")) != -1) {
        switch (opt) {
            case 'm': {
                // 2.有-m及选项参数：打印参数
                printf("msg: %s\n", optarg);
            } break;
            default: {
                // 3.选项不合法：友好提示
                fprintf(stderr, "Usage: %s [-m msg]\n", argv[0]);
                exit(1);
            }
        }
    }
    return 0;
}
```
## head.h

```c++
#ifndef _HEAD_H
#define _HEAD_H
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <string.h>
#endif
```
## test.sh

```shell
#!/bin/bash
cmd="./git_commit"
msg="first commit"
echo "1)${cmd}:" | lolcat
${cmd}
echo "2)${cmd} -m \"${msg}\":" | lolcat
${cmd} -m "${msg}"
echo "3)${cmd} -m:" | lolcat
${cmd} -m
echo "3)${cmd} -b:" | lolcat
${cmd} -b
```
* ./git_commit的git_commit是使用gcc ... -o git_commit生成的可执行文件名
# 参考

* 主要知识点参考《网络与系统编程》
    * [0 课程介绍及命令行解析函数](https://doublelll3.ml/wxb_0_%E8%AF%BE%E7%A8%8B%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%A7%A3%E6%9E%90%E5%87%BD%E6%95%B0/)——getopt
    * [1 文件、目录操作与实现ls的思路](https://doublelll3.ml/wxb_1_%E6%96%87%E4%BB%B6%E3%80%81%E7%9B%AE%E5%BD%95%E6%93%8D%E4%BD%9C%E4%B8%8E%E5%AE%9E%E7%8E%B0ls%E7%9A%84%E6%80%9D%E8%B7%AF/)——open、read
    * [3 多进程](https://doublelll3.ml/wxb_3_多进程/)——fork、wait、exec族⭐
