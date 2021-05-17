---
title: '1 尝尝鲜：从一个Hello到另一个Hello'
date: 2021-05-15 09:40:00
tags: [计算机,操作系统,极客时间]
published: true
hideInList: true
feature: 
isTop: false
---

***环境：Windows下的Ubuntu 18.04虚拟机***

# 实践一下

🎯目标：让自己的系统输出"Hello OS"

## 下载源码

1. 可在系统任意位置，**打开终端**执行以下命令，下载源码到虚拟机上
```shell
git clone https://gitee.com/lmos/cosmos.git
```
## 生成bin文件

2. 在库中具体位置：/lesson02/HelloOS下，编译源码，生成HelloOS.bin
* 首先，需要安装工具gcc、make、nasm，默认只有nasm没有，其它工具如没有再安装
```shell
sudo apt update
sudo apt install nasm
```
* 然后，执行编译即可，默认按照Makefile里的规则，**生成HelloOS.bin**
```shell
make
```
## 移动bin文件

3. 将HelloOS.bin放到/boot/目录下
```shell
mv HelloOS.bin /boot/
```
## 修改grub启动配置⭐

4. 修改/boot/grub/grub.cfg文件
* 使用sudo权限，用vim打开
```shell
sudo vim /boot/grub/grub.cfg
```
* 在最后位置插入以下代码，注意修改两个地方：set root、multiboot2
```json
menuentry 'HelloOS' {
     insmod part_msdos            # GRUB加载分区模块识别分区
     insmod ext2                  # GRUB加载ext文件系统模块识别ext文件系统
     set root='hd0,msdos4'        # 注意boot目录挂载的分区
     multiboot2 /boot/HelloOS.bin # GRUB以multiboot2协议加载HelloOS.bin
     boot                         # GRUB启动HelloOS.bin
}
```
* 修改内容参考此步骤，再打开一个终端，观察df /boot/的结果，关注两个地方：文件系统、挂载点

<img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/image-20210515092657939.png" alt="image-20210515092657939" style="zoom:80%;" />

* 文件系统⭐
    * 若为/dev/sda1，即1对应修改set root='hd0,msdos1'
    * 如果不是sda [其它系统环境]，还需看情况修改 [看课程评论]
* 挂载点⭐
    * 若为/boot，对应修改multiboot2 /HelloOS.bin
    * 若为/，则无需修改

PS：使用`:w!`保存，使用`:q`退出编辑

<img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/image-20210515092718504.png" alt="image-20210515092718504" style="zoom:67%;" />

## 重启机器

5. 重启机器，在加载系统前疯狂按shift键 [也可能是其他键，如esc键]，进入grub引导界面

<img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/image-20210515092730798.png" alt="image-20210515092730798" style="zoom: 80%;" />

* 选择自己的系统，按enter键

<img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/image-20210515092740876.png" alt="image-20210515092740876" style="zoom:80%;" />

* 如果此处报错，主要涉及系统类型（物理机、其它虚拟机系统）、系统分区类型（UEFI）、分区表类型（GPT）等因素，可参考以下参考内容👇
# 参考内容

如果在其他环境下运行，可参考课程里的评论和相关博客

* [操作系统实战45讲01：运行HelloOS](https://blog.csdn.net/chenchengwudi/article/details/116707122?spm=1001.2014.3001.5501&fileGuid=KrkEVnreWxFr4eAJ)——CSDN
* [pedro/pos](https://gitee.com/gaopedro/pos/tree/master?fileGuid=KrkEVnreWxFr4eAJ)——码云
    * 可在非桌面端（如WSL2）下实现
* [彭东的【操作系统实战】交流群核心问题汇总](https://shimo.im/docs/PgF2AVpTWWg7LPAR/?fileGuid=KrkEVnreWxFr4eAJ)——石墨
    * ❗ 这里基本都能找到答案！
* [02 | 几行汇编几行C：实现一个最简单的内核](https://time.geekbang.org/column/article/369502?fileGuid=KrkEVnreWxFr4eAJ)——极客时间

# 课程链接

扫码即可加入：

<img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/image-20210515092755797.png" alt="image-20210515092755797" style="zoom: 25%;" />