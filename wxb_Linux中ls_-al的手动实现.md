---
title: 'Linux中ls -al的手动实现'
date: 2021-01-14 10:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: false
feature: 
isTop: false
---
# 功能要求

* 实现与Linux原生命令【ls -al】类似的效果
* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595304091-Q9eE7dhxdoX4rYy7.png" alt="图片" style="zoom:67%;" />
* 需要的信息有：文件信息、连接数、用户名、组名、文件大小、修改时间、文件名
* 附加实现：文件排序、颜色美化、软连接显示
# 最终效果

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595302482-XcEaCzBRRSl5em3C.png" alt="图片" style="zoom:67%;" />
* 已实现ls -al的基本模板
# 实现过程

## 思路流程图

*  <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610849380337-image-20210117100933677.png" alt="image-20210117100933677" style="zoom:50%;" />

## 获取命令行参数

捕捉ls的选项-al和可选的选项参数path

* 识别ls -al、ls -l、ls -al path、ls -l path、其他报错
```c++
#include "head.h"
void myls(char **argv, char *path, int a_flag) {
    if (a_flag) printf("run %s -al %s\n", argv[0], path);
    else printf("run %s -l %s\n", argv[0], path);
    return ;
}
void show_tips(char **argv) {
    fprintf(stderr, "Usage: %s -[a]l [path]\n", argv[0]);
    return ;
}
int main(int argc, char **argv) {
    // 不带路径参数 [默认为当前目录]
    if (argc == 2){
        if (!strcmp(argv[1], "-al")) myls(argv, ".", 1);
        else if (!strcmp(argv[1], "-l")) myls(argv, ".", 0);
        else show_tips(argv), exit(1);
        return 0;
    }
    int opt;
    int a_flag = 0;  // 判断-a选项
    // 带路径参数
    while ((opt = getopt(argc, argv, "al:")) != -1) {
        switch (opt) {
            case 'a': {
                a_flag = 1;
            } break;
            case 'l': {
                myls(argv, optarg, a_flag);
            } break;
            default: {
                show_tips(argv);
                exit(1);
            }
        }
    }
    return 0;
}
```
* 因为getopt中对于可选选项参数的选项【选项后跟"::"】，其参数必须跟在其后【如ls -al123】，而不能像原生ls -al path那样将选项和选项参数用空格隔开【如ls -al 123】，所以对于选项的参数设置为必选【选项后跟":"】，再在前面做一个无选项参数时的判断
* 头文件head.h见末尾
* 使用测试脚本test.sh [见末尾] 测试，效果如下：
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595299810-LSEn7roV5Y5PQslI.png" alt="图片" style="zoom: 50%;" />
    * 已经可以捕捉识别ls -al、ls -l、ls -al path、ls -l path，以及其他错误输入
## 读取目录

读取出目录的文件名列表

* 使用opendir、readdir [后面为了排序方便换用了scandir，见排序显示]
* 只需修改myls()函数，代码如下
```c++
void myls(char *path, int a_flag) {
    DIR *dirp;
    // 0. 打开并读取目录
    if ((dirp = opendir(path)) != NULL) {
        struct dirent *dent;
        while (dent = readdir(dirp)) {
            if (dent->d_name[0] == '.' && !a_flag) continue;
            printf("%s\n", dent->d_name);
        }
    } else {
        perror("opendir");
        exit(1);
    }
    return ;
}
```
* 此时myls已经不需要argv参数
* 根据是否有a选项，判断是否显示隐藏文件
    * 只要文件名以"."开头，则为隐藏文件
* 部分测试效果如下
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595296764-WKZVSWtgUjRNMf7g.png" alt="图片" style="zoom:67%;" />
    * 已经可以读取出目录里所有文件的名称，并区分出隐藏文件
## 读取并处理文件信息⭐

根据每个文件的名称，获取文件的信息

### lstat获取文件基本信息

1. 使用【lstat】获取文件基本信息【文件权限、硬连接数、uid、gid、文件大小、修改时间】

* 需要为创建的stat结构体分配好内存，而**不是创建结构体指针**，参考[C - linux stat函数中struct stat * buffer和＆buffer有什么区别](https://www.coder.work/article/1876723)
* 对于不是当前路径下的目录或文件，需要使用**路径拼接**后的绝对路径访问 [或者chdir直接切换到指定目录下]
    * 否则lstat无法定位文件路径
```c++
// 路径拼接，获得绝对路径
void absolute_path(char *ab_path, char* path, char *filename) {
    strcpy(ab_path, path);                            // 保留原path值
    strcat(ab_path, "/");                             // 注意添加路径分隔符
    strcat(ab_path, filename);                        // 拼接
    return ;
}
```
### 文件信息的友好显示

2. 文件信息<文件类型、文件[特殊]权限> 👉 字符形式

```c++
// 获取友好的文件信息
void mode2str(mode_t smode, char *mode) {
    int i = 0;
    // rwx数组，分别对应000、001、...、110、111
    char *rwx[] = {
        "---", "--x", "-w-",
        "-wx", "r--", "r-x",
        "rw-", "rwx"
    };
    // 获取文件类型
    if (S_ISREG(smode)) mode[0] = '-';
    else if (S_ISDIR(smode)) mode[0] = 'd';
    else if (S_ISBLK(smode)) mode[0] = 'b';
    else if (S_ISCHR(smode)) mode[0] = 'c';
#ifdef S_ISFIFO
    else if (S_ISFIFO(smode)) mode[0] = 'p';
#endif
#ifdef S_ISLNK
    else if (S_ISLNK(smode)) mode[0] = 'l';
#endif
#ifdef S_ISSOCK
    else if (S_ISSOCK(smode)) mode[0] = 's';
#endif
    else mode[i++] = '?';
    // 获取文件权限
    strcpy(&mode[1], rwx[(smode >> 6) & 7]);
    strcpy(&mode[4], rwx[(smode >> 3) & 7]);
    strcpy(&mode[7], rwx[smode & 7]);
    // 获取文件特殊权限
    if (smode & S_ISUID) mode[3] = (smode & S_IXUSR) ? 's' : 'S';
    if (smode & S_ISGID) mode[6] = (smode & S_IXGRP) ? 's' : 'S';
    if (smode & S_ISVTX) mode[9] = (smode & S_IXOTH) ? 't' : 'T';
    return ;
}
```
* 思路，参考[Printing file permissions like 'ls -l' using stat(2) in C](https://stackoverflow.com/questions/10323060/printing-file-permissions-like-ls-l-using-stat2-in-c)——StackOverflow
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595293851-PEKgJwi3Pg8ut9M1.png" alt="图片" style="zoom: 67%;" />
    * 本文用的是第2个回答的解法，开辟了rwx数组，巧用位运算
* 细节，参考[stat 结构体 st_mode 字段](https://blog.csdn.net/q1007729991/article/details/53377074)、[Linux中用st_mode判断文件类型](https://blog.csdn.net/simmerlee/article/details/8281399)——CSDN
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595290834-er0b5v6G3hhOmcs3.png" alt="图片" style="zoom:67%;" />
    * 每一位的含义很清晰
* 宏定义，可参考man手册：man inode，搜索st_mode
### 修改时间的友好显示

3. 时间戳 👉 友好可读的时间

```c++
// 获取友好的mtime
void mtim2str(struct timespec *mtim, char *str, size_t max) {
    struct tm *tmp_time = localtime(&mtim->tv_sec);           // 转换为当地日历时间
    strftime(str, max, "%b %e %H:%M", tmp_time);              // 转换为指定格式
    return ;
}
```
* 时间戳→时间结构体→指定格式
    * 通过localtime—[cppreference](https://en.cppreference.com/w/c/chrono/localtime)完成第一步转换，得到tm结构体
    * 通过strftime—[cppreference](https://en.cppreference.com/w/c/chrono/strftime)完成第二步转换，得到字符串
* 参考[C语言中的时间戳和时间格式](https://www.jianshu.com/p/8ff0ec67e34b)——简书
* [亦或] 参考man手册的例子，使用ctime—[cppreference](https://en.cppreference.com/w/c/chrono/ctime)，再提取子字符串
### 获取所属用户名、组名

4、5. 通过【getpwuid、getgrgid】与uid、gid获得文件所属的【用户名、组名】

```c++
strcpy(uname, getpwuid(st.st_uid)->pw_name);  // 4. 获取文件所属者名称
strcpy(gname, getgrgid(st.st_gid)->gr_name);  // 5. 获取文件所属组名称
```
* 效果如下：
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595288271-mNWoAa1zJfRLRcDT.png" alt="图片" style="zoom:67%;" />
    * 只有想不到的工具，当然也可以尝试自己实现这个通过uid、gid获取名称的过程
## 排序显示

* 详见文末完整代码
* readdir读取目录文件的时候是乱序了，而**scandir**可以按字典序读取出文件列表
* 参考man scandir的EXAMPLE
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595286510-Vf8LRTNcoKXwSEFJ.png" alt="图片" style="zoom: 50%;" />
    * 创建文件列表，按字典序读入，逆序输出
* scandir完成了opendir和readdir两步工作，所以将opendir与readdir替换为sancdir即可，后面同样是将d_name给lstat使用
* 效果如下
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595284725-mXTuTCOqIrT5Q5Ot.png" alt="图片" style="zoom:67%;" />
    * 排序规则稍有区别，接下来控制颜色和显示软连接指向
## 颜色控制

* 详见文末完整代码 [颜色宏见head.h]
* 主要对目录、软连接、可执行文件、部分特殊权限文件的颜色进行了美化
    * 其他颜色大同小异
* 效果如下
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595282448-LZ5VDZkdUNeU1HeR.png" alt="图片" style="zoom:67%;" />
    * 再实现软连接指向的显示
## 软连接指向的显示

* 详见文末完整代码
* 使用readlink读取软连接指向的源文件名，参考man readlink
* 效果如下
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595279918-7zQvsrwrifFtvK5O.png" alt="图片" style="zoom:67%;" />
    * 🔚
# 完整代码

## myls.c

```c++
#include "head.h"
// 路径拼接，获得绝对路径
void absolute_path(char *ab_path, char* path, char *filename) {
    strcpy(ab_path, path);                            // 保留原path值
    strcat(ab_path, "/");                             // 注意添加路径分隔符
    strcat(ab_path, filename);                        // 拼接
    return ;
}
// 获取友好的文件信息
void mode2str(mode_t smode, char *mode, char *color) {
    int i = 0;
    // rwx数组，分别对应000、001、...、110、111
    char *rwx[] = {
        "---", "--x", "-w-",
        "-wx", "r--", "r-x",
        "rw-", "rwx"
    };
    // 获取文件类型
    if (S_ISREG(smode)) mode[0] = '-';
    else if (S_ISDIR(smode)) mode[0] = 'd', strcpy(color, "BLUE_HL");
    else if (S_ISBLK(smode)) mode[0] = 'b';
    else if (S_ISCHR(smode)) mode[0] = 'c';
#ifdef S_ISFIFO
    else if (S_ISFIFO(smode)) mode[0] = 'p';
#endif
#ifdef S_ISLNK
    else if (S_ISLNK(smode)) mode[0] = 'l', strcpy(color, "BLUE");
#endif
#ifdef S_ISSOCK
    else if (S_ISSOCK(smode)) mode[0] = 's';
#endif
    else mode[i++] = '?';
    // 获取文件权限
    strcpy(&mode[1], rwx[(smode >> 6) & 7]);
    strcpy(&mode[4], rwx[(smode >> 3) & 7]);
    strcpy(&mode[7], rwx[smode & 7]);
    // 获取文件特殊权限
    if (smode & S_ISUID) mode[3] = (smode & S_IXUSR) ? 's' : 'S';
    if (smode & S_ISGID) mode[6] = (smode & S_IXGRP) ? 's' : 'S';
    if (smode & S_ISVTX) mode[9] = (smode & S_IXOTH) ? 't' : 'T';
    // + 配置颜色 [上面也有]
    if (mode[0] == '-') {
        if (strstr(mode, "x")) strcpy(color, "GREEN_HL");
        if (strstr(mode, "s") || strstr(mode, "S")) strcpy(color, "YELLOW_BG");
    }
    return ;
}
// 获取友好的mtime
void mtim2str(struct timespec *mtim, char *str, size_t max) {
    struct tm *tmp_time = localtime(&mtim->tv_sec);       // 转换为当地日历时间
    strftime(str, max, "%b %e %H:%M", tmp_time);          // 转换为指定格式
    return ;
}
void myls(char *path, int a_flag) {
    // 0. 按 [字典序] 读取出目录的文件列表
    struct dirent **namelist;
    int n, i = -1;
    n = scandir(path, &namelist, NULL, alphasort);        // 按字典序读
    if (n == -1) {
        perror("scandir");
        exit(1);
    }
    // 正序遍历文件列表
    while (i < n - 1) {
        i++;
        struct dirent *dent = namelist[i];
        if (dent->d_name[0] == '.' && !a_flag) continue;  // 是否显示隐藏文件
        struct stat st;                                   // 需要为该结构体开辟内存
        char ab_path[128];                                // 记录绝对路径
        absolute_path(ab_path, path, dent->d_name);       // +. 路径拼接
        // 1. 读取文件信息
        if (!lstat(ab_path, &st)) {
            char mode[16], mtime[32], uname[16], gname[16], filename[512], color[16] = {0};
            mode2str(st.st_mode, mode, color);            // 2. 处理文件信息 [并设置颜色]
            mtim2str(&st.st_mtim, mtime, sizeof(mtime));  // 3. 处理修改时间
            strcpy(uname, getpwuid(st.st_uid)->pw_name);  // 4. 获取文件所属者名称
            strcpy(gname, getgrgid(st.st_gid)->gr_name);  // 5. 获取文件所属组名称
            // 打印除文件名外的基本信息
            printf("%s %lu %s %s %*lu %s ",
                    mode, st.st_nlink, uname, gname,
                    5, st.st_size, mtime
                  );
            // 6. 根据颜色包装文件名
            if (!strcmp(color, "BLUE")) sprintf(filename, BLUE("%s"), dent->d_name);
            else if (!strcmp(color, "BLUE_HL")) sprintf(filename, BLUE_HL("%s"), dent->d_name);
            else if (!strcmp(color, "GREEN_HL")) sprintf(filename, GREEN_HL("%s"), dent->d_name);
            else if (!strcmp(color, "YELLOW_BG")) sprintf(filename, YELLOW_BG("%s"), dent->d_name);
            else strcpy(filename, dent->d_name);
            // 7. 对软连接特殊处理
            if (mode[0] == 'l') {
                char linkfile[32];
                readlink(ab_path, linkfile, 32);
                strcat(filename, " -> ");
                strcat(filename, linkfile);
            }
            // 单独打印文件名称 [含颜色]
            printf("%s\n", filename);
        } else {
            perror("lstat");
            exit(1);
        }
        free(namelist[i]);
    }
    free(namelist);
    return ;
}
void show_tips(char **argv) {
    fprintf(stderr, "Usage: %s -[a]l [path]\n", argv[0]);
    return ;
}
int main(int argc, char **argv) {
    // 不带路径参数 [默认为当前目录]
    if (argc == 2){
        if (!strcmp(argv[1], "-al")) myls(".", 1);
        else if (!strcmp(argv[1], "-l")) myls(".", 0);
        else show_tips(argv), exit(1);
        return 0;
    }
    int opt, a_flag = 0;  // a_flag：判断-a选项
    // 带路径参数
    while ((opt = getopt(argc, argv, "al:")) != -1) {
        switch (opt) {
            case 'a': {
                a_flag = 1;
            } break;
            case 'l': {
                myls(optarg, a_flag);
            } break;
            default: {
                show_tips(argv);
                exit(1);
            }
        }
    }
    return 0;
}
```
* 详见注释
* 列间距可通过printf("%\*s", len, "xyz")形式控制，用len控制\*的值，len根据字符串长度调整 [未考虑]
* 排序显示是将opendir + readdir的方式改为了scandir，后者可以将目录下的文件按顺序读入到一个字符串数组
* 颜色控制
    * ❗ 在mode2str里设置颜色时，color字符数组的赋值用strcpy的方式，如果直接用"="赋值的话，color在函数里赋的值出不了函数，即使color是字符指针也如此。所以用color字符数组与strcpy函数 [这里color采用的是传出参数的方式]
    * 两个字符串判等使用strcmp，而不是"=="
    * 为了方便文件名颜色和文件名的绑定，创建一个**512**字节大小的文件名字符数组filename封装文件名和对应的颜色
        * 使用512是因为sprintf中的%s，编译器认为最高可达256字节
        * 🆒 有更优美的解决方案——使用snprintf，并根据数据动态开辟空间，参考[Detecting String Truncation with GCC 8](https://developers.redhat.com/blog/2018/05/24/detecting-string-truncation-with-gcc-8/)
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/1610595269683-IuYY5JHXCHCx7hBy.png" alt="图片" style="zoom:67%;" />
            * malloc是精髓
* 软连接指向的显示
    * filename的产生源于此，指向的箭头和指向的原文件的显示为默认颜色
## head.h

```c++
#ifndef _HEAD_H
#define _HEAD_H
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <dirent.h>
#include <sys/stat.h>
#include <time.h>
#include <pwd.h>
#include <grp.h>
#define COLOR(a, b) "\033[" #b "m" a "\033[0m"
#define COLOR_BG(a, b) "\033[2;" #b "m" a "\033[0m"
#define COLOR_HL(a, b) "\033[1;" #b "m" a "\033[0m"
#define RED(a) COLOR(a, 31)
#define GREEN(a) COLOR(a, 32)
#define YELLOW(a) COLOR(a, 33)
#define BLUE(a) COLOR(a, 34)
#define PURPLE(a) COLOR(a, 35)
#define RED_HL(a) COLOR_HL(a, 31)
#define GREEN_HL(a) COLOR_HL(a, 32)
#define YELLOW_HL(a) COLOR_HL(a, 33)
#define BLUE_HL(a) COLOR_HL(a, 34)
#define PURPLE_HL(a) COLOR_HL(a, 35)
#define RED_BG(a) COLOR_BG(a, 41)
#define GREEN_BG(a) COLOR_BG(a, 42)
#define YELLOW_BG(a) COLOR_BG(a, 43)
#define BLUE_BG(a) COLOR_BG(a, 44)
#define PURPLE_BG(a) COLOR_BG(a, 45)
#endif
```
## test.sh

```shell
#!/bin/bash
path="x.TestDir"
echo "./ls -al:" | lolcat
./ls -al
echo "./ls -l:" | lolcat
./ls -l
echo "./ls -al $path:" | lolcat
./ls -al $path
echo "./ls -l $path:" | lolcat
./ls -l $path
echo "./ls -b:" | lolcat
./ls -b
echo "./ls -b $path:" | lolcat
./ls -b $path
```
* ./ls的ls是使用gcc ... -o ls生成的可执行文件名

[PS]

* 如何查看复杂结构体的成员变量对应什么样的格式控制字符串？
    * 如%s、%lu...
    * ① 先随便写一个，看报错信息的提示
    * ② 或者，使用ctags查看源码：ctrl + ]
# 参考

* 整体思路参考：[《1 文件、目录操作与实现ls的思路——实现ls-al的基本思路》](https://doublelll3.ml/wxb_1_%E6%96%87%E4%BB%B6%E3%80%81%E7%9B%AE%E5%BD%95%E6%93%8D%E4%BD%9C%E4%B8%8E%E5%AE%9E%E7%8E%B0ls%E7%9A%84%E6%80%9D%E8%B7%AF/#%E5%AE%9E%E7%8E%B0ls-al%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%80%9D%E8%B7%AF)
