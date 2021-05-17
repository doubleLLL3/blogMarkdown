---
title: '文件特殊权限的常用场景'
date: 2020-12-14 19:00:00
tags: [计算机,操作系统,Linux,海贼班]
published: true
hideInList: false
feature: 
isTop: false
---
**实验环境**：Ubuntu18.04远程服务器 + WSL 2本机

# 测评说明

[版本一]

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Vw6qCLa.png" alt="图片" style="zoom: 67%;" />

[版本二]

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/hseZWVa.png" alt="图片" style="zoom:67%;" />

* ⭐考察对文件权限【尤其是文件特殊权限】的理解
    * 文件权限：参考《Linux入门及使用》笔记汇总——3 Linux基础知识——[用户和组](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E7%94%A8%E6%88%B7%E5%92%8C%E7%BB%84)以及[文件权限的修改](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E6%96%87%E4%BB%B6%E6%9D%83%E9%99%90%E7%9A%84%E4%BF%AE%E6%94%B9)
    * 文件特殊权限：参考《Linux入门及使用》笔记汇总——8 文件与目录、AWK——[文件的特殊权限](https://doublelll3.ml/lnxrm_8_%E6%96%87%E4%BB%B6%E4%B8%8E%E7%9B%AE%E5%BD%95%E3%80%81AWK/#%E6%96%87%E4%BB%B6%E7%9A%84%E7%89%B9%E6%AE%8A%E6%9D%83%E9%99%90)
* 理解了以上，实现上述功能就游刃有余了
# 最终效果

* 其他用户无法进入Project目录

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/uxMDsfp.png" alt="图片" style="zoom:67%;" />

* TestUser1创建了文件u1.txt

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/iu4neAY.png" alt="图片" style="zoom:67%;" />

* TestUser2仍可以编辑u1.txt，但无法删除它

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/YoWpkPS.png" alt="图片" style="zoom:67%;" />

* 
   * 反之同理

---


# 实现过程

## 准备工作

* 新建一个用户组TestGroup，使用groupadd命令，有一些选项可查看man手册
```shell
groupadd TestGroup
```
* 新建两个属于TestGroup组的用户TestUser1、TestUser2
```shell
useradd -G TestGroup TestUser1
useradd -G TestGroup TestUser2
```
*   * 需要先有组
* 设置密码，用来登录用户，否则登不了
```shell
passwd TestUser1
--->输入密码：xxx
passwd TestUser2
--->输入密码：yyy
```
* 在/opt目录下新建Project目录，作为项目目录
```shell
cd /opt
mkdir Project
```
## 实现功能1 进入权限

【只能TestUser1、TestUser2、 root三个用户进入该目录】

* 把Project目录的所属组修改为TestGroup，所属用户不动
```shell
sudo chown :TestGroup Project
```
* 将其他用户的执行权限去除，其他用户将不能进入目录
```shell
sudo chmod o-x Project
```
* 效果如下：

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/nlubPF9.png" alt="图片" style="zoom:67%;" />

## 实现功能2 编辑权限

【TestUser1创建的文件，TestUser2可以编辑】

* 首先，给所属组TestGroup添加写权限，这样用户才能在该目录下创建文件
```shell
sudo chmod g+w Project
```
* 设置set_gid，这样用户进入目录后的操作会以目录所属组的身份进行
```shell
sudo chmod g+s Project
```
[PS] 否则，用户创建的文件的所属组属于与自己同名的组，而不是TestGroup [从而无法将两个用户建立联系]
* 权限如下：

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/70b6JmL.png" alt="图片" style="zoom:67%;" />

## 实现功能3 删除权限

【TestUser1和TestUser2只能删除自己创建的文件】

* 设置sticky bit，这样在该目录下，用户只能删除自己创建的内容
```shell
sudo chmod +t Project
```
* 权限如下：

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/mxV6phw.png" alt="图片" style="zoom:67%;" />

[PS] 文件夹颜色都变了，zsh干的漂亮


---


# 思考点

* 某场景：进入某目录的用户对某文件没有写权限，却可以删除文件，为什么？
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/ghYqP4q.png" alt="图片" style="zoom:67%;" />
    * 删除文件的权限：优先取决于用户对进入的目录的权限
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/TfLSIS9.png" alt="图片" style="zoom:67%;" />
        * 该用户属于TestGroup，有可写权限
        * 参考[鸟哥的私房菜——目录与文件之权限意义](http://linux.vbird.org/linux_basic/0210filepermission.php#filepermission_dir)——权限对目录的重要性：
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Lg2GhLI.png" alt="图片" style="zoom:67%;" />
            * 但是碰见sticky bit [黏着位]，事情又不一样了
# 附加

* 目录的可执行权限代表进入权限

---


# 参考资料

* 《Linux入门及使用》笔记汇总——3 Linux基础知识——[用户和组](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E7%94%A8%E6%88%B7%E5%92%8C%E7%BB%84)以及[文件权限的修改](https://doublelll3.ml/lnxrm_3_Linux%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/#%E6%96%87%E4%BB%B6%E6%9D%83%E9%99%90%E7%9A%84%E4%BF%AE%E6%94%B9)
* 《Linux入门及使用》笔记汇总——8 文件与目录、AWK——[文件的特殊权限](https://doublelll3.ml/lnxrm_8_%E6%96%87%E4%BB%B6%E4%B8%8E%E7%9B%AE%E5%BD%95%E3%80%81AWK/#%E6%96%87%E4%BB%B6%E7%9A%84%E7%89%B9%E6%AE%8A%E6%9D%83%E9%99%90)
* [鸟哥的私房菜——目录与文件之权限意义](http://linux.vbird.org/linux_basic/0210filepermission.php#filepermission_dir)
