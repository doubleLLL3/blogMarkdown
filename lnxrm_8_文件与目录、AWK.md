---
title: '8 文件与目录、AWK'
date: 2020-12-02 18:00:00
tags: [计算机,Linux,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
文件读取、权限控制、AWK文本处理

# 课程内容

## 目录

* cd：切换工作目录
    * 参数缺省：回到自己的家，[PS] C语言不支持缺省参数，可通过宏实现
    * 参数~：回到自己的家，可使用 [~用户名] 的方式指定该用户名的家
    * 参数-：回到上次目录，适合两个长路径的相互切换
* pwd
    * -L 逻辑工作目录
    * -P 物理工作目录【真实】
    * 主要体现在**软连接**上
        * ln -s [要连接的文件] [软连接的位置/ 或 软连接的位置/软连接名]
        * 两者在同一片物理空间，物理目录一致
        * 【删除】对于软连接test指向一个文件夹
            * [rm test] 只删除这个连接文件
            * [rm -r test/] 会删除连接的文件夹里的所有内容，危险！
                * 虽然该操作会提示test/不是一个目录，但test/下已经干干净净
                * 如果连接的是文件不是文件夹，不影响原内容
        * [PS] 硬连接不能用在目录上
* mkdir
    * -p 自动创建父目录，当想创建多级目录时
    * -m 设置权限
    * 示例：mkdir -p -m 700 ./test/abc/x
* rmdir：可用rm替代
* 绝对路径：从根目录/开始
* 相对路径：从当前目录.或上层目录..开始
## 文件与目录的管理

* ls：显示文件及目录信息
* cp：拷贝
    * -i 若文件存在，询问
    * -r 递归
    * -a = -pdr
        * p 拷贝时连同文件属性
        * d 拷贝连接文件而不是其指向
        * r 递归
    * -u 源文件比目标文件新才拷贝，适合大容量备份
    * -s 拷贝为软连接；-l 拷贝为硬连接
    * cp/mv 多个文件时，最后一个必须是目录
    * ⭐cp的实现逻辑
        * 打开文件 → 读文件 → 写文件
        * 【如果cp的是一个管道】
            * 那需要从另一端传入数据，才能读取数据再写
            * cp完后，拷贝结果为存储管道数据的普通文件
* rm：删除
    * -i 互动模式，会询问
    * -r 递归
    * -f 强制
* mv：移动
    * 本质就是cp + rm
    * -i、-f、-u，类似cp
* basename：取文件名；dirname：取目录名
    * 不会判断有没有文件
    * basename /home/xxx/abc → abc
    * dirname /home/xxx/abc → /home/xxx
    * 一般在shell脚本中用
* rename：可以被cp、mv取代
## 文件内容的查阅

* cat：正向连续读 [反向：tac]
    * -A = -vET
        * -v 显示一般看不到的符号
        * -E 显示断行符为$ [同vim下:set list、:set nolist]
        * -T 显示TAB为^I
        * [PS] 如果有\0...stray错误（拷贝别人代码时），可以用-A看看特殊字符
    * -b 列出行号；-n 连同空行列出行号
* nl：输出行号显示文件
    * 选项 [选项参数]👈它俩有顺序讲究
    * -b [a/t] 行号指定的方式 → 对应[cat -n/ cat -b]
    * -n [ln/rn/rz] 列出行号的表示方法
    * -w [num] 行号所占位数
    * 一般用于特殊文本
* more、less、head、tail
    * more 和 less 的区别
        * more只能往下翻，搜索没有高亮
        * less更灵活，可以上下翻，搜索有高亮
    * 取21 - 40行：man ls | head -n 40 | tail -n -20 【去尾、掐头，sed也能实现】
* od 以二进制方式查看文件内容，暂时用不到，具体操作见课程速记
## 修改文件时间与新建文件

* 三个时间
    * mtime [modify]：修改时间【默认显示】
    * ctime [change]：权限修改时间
    * atime [access]：访问时间（记录成本很高）
    * [PS] Mac下有更多的时间类型，与Linux不兼容
* touch 文件
    * 主要用来修改时间
    * 如果不存在，会自动创建
    * 特殊用法用的少，详见课程速记
    * [PS] 可以把文件的时间改回去，不让人感知
## 文件隐藏属性

可通过chattr [+/-选项] 更改

* A：不修改atime [可提高效率、磁盘寿命]
* S：同步写入
    * 指IO同步，避免断电没保存 [断电后内存里的数据直接消失]
* a：只能增加数据 [日志]
    * 不能修改，不能删除，即使是sudo
    * 在vim下不能修改，但修改[不用sudo]会产生没有a属性的备份文件
* i：不能删除、修改、建立连接[默认为硬连接]，相当于【固化】
* s：文件删除时，直接从磁盘删除
    * 正常情况下，删除文件，只是把文件与对应磁盘位置的关系解除了，内容其实还在磁盘里，有点像C语言的free()
    * 加了s属性后，删除文件会将磁盘里的数据全部置0，真正的重写清空
    * 这里一般指机械硬盘，固态硬盘又另说
* 【lsattr】查看文件隐藏属性
    * [PS] 软连接似乎没有隐藏属性
## 文件的特殊权限

|权限|权限占位符|作用对象|效果|
|:----:|:----:|:----:|:----|:----:|:----|:----:|:----|
|set_uid|s|二进制程序文件，非脚本|执行该程序时获得程序所有者权限|
|set_gid|s|二进制程序文件，目录|在该目录下，有效组变为目录所属组|
|sticky bit|t|目录|在该目录下，用户只能删除自己创建的内容|

可通过chmod [u/g/空 |+/-|s/t] 更改

* set_uid
    * ⭐chmod u+s，针对所属用户权限
    * 占所属用户权限的x的位置：如果存在x，则变为s；不存在，则变为S
    * 举例：其他用户通过passwd命令修改密码
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/qZ5cj16.png)
        * 其他用户对存放密码的文件/etc/shadow没有任何权限
        * 但passwd命令有s权限，所以其他有执行权限的用户在执行时获得程序所有者权限，从而可以修改密码文件/etc/shadow
* set_gid
    * ⭐chmod g+s，针对所属组权限
    * 占所属组权限的x的位置：如果存在x，则变为s；不存在，则变为S
    * 在这个目录里做的事情都是以【目录所属组】的身份做的
    * 一般作用于目录
        * 而对于二进制程序文件，有点类似set_uid，但获得的是【所属**组**】的权限
    * 方便协同办公，以组为单位管理目录下的所有用户
* sticky bit 黏着位
    * ⭐chmod +t，可参考[粘滞位](https://www.wikiwand.com/zh/%E7%B2%98%E6%BB%9E%E4%BD%8D)——维基百科
    * 占其它用户权限的x的位置：如果存在x，则变为t；不存在，则变为T
    * 举例：/tmp目录及目录下的一些目录
## 命令与文件的查询

* which：寻找可执行文件
    * 在PATH路径下查找
    * 在zsh下ls是ls --color=tty的别名，它会被第二条命令的 ls 误认为是多个路径
        * ![图片](https://gitee.com/doubleL3/blog-imgs/raw/master/img/pnUcovI.png)
        * 查看which的选项，可以通过which -p ls 对 ls 只做【路径搜索】
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/HlXZyg0.png" alt="图片" style="zoom:80%;" />
            * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/AUPGGrx.png" alt="图片" style="zoom:80%;" />
            * 学会使用命令替换符
* whereis：寻找更多类型的文件，可指定特定类型
    * -b 只查找二进制文件
    * -m 只查找man手册中的文件
    * -s 只查找源文件
    * -u 查找其它文件
* locate：模糊定位
    * -i 忽略大小写
    * 基于数据库，不是实时更新的，可使用[sudo] updatedb马上更新
### **find：高级查找**⭐

【时间】

* -mtime、-ctime、-atime [单位为天]
    * <--\_\_+n\_\_\_|\_\_n\_\_|\_\_-n\_\_|：`n天之前 [不包含n] | n天前的一天内 | n天内 [包含n]`
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/image-20201216223627330.png" alt="image-20201216223627330" style="zoom: 50%;" />
    * 分钟为单位可查看man手册：-mmin、-cmin、-amin
* ⭐创建一个文件，文件及当前目录的mtime都会被修改
    * 因为每个目录有一张文件表，里面记录文件名和inode
    * 当目录里增加了一个文件，该文件表也就多了一个文件名及inode
    * 所以当前目录的mtime也会变
    * ❗ 但如果只是修改一个文件，很好理解，当前目录的mtime不会改变
* -newer file 以文件时间作为时间节点

【用户、用户组】

* -user 指定user名
    * 查找用户hz在10分钟内活跃的进程ID 【PID】
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/0E75py5.png" alt="图片" style="zoom:67%;" />
    * 进程文件在/proc下，都是虚拟的，文件[目录]大小为0
    * 应用：杀死进程
    * [PS] 进程ID和inode不是一回事，不能杀inode
* -uid、-gid、-group、nouser、nogroup的使用同-user

【文件、文件权限】

* -name 匹配文件名
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/8WVmXXJ.png" alt="图片" style="zoom:50%;" />
    * \( ... -o ... \) 逻辑或，注意括号用\转义，两端里面有空格
    * xargs——参数代换，不需要再移到前面用命令替换符
* -size [+大于-小于]
    * 可以查找空文件，用来做清理，但要注意一些特殊文件[.py等]
* -type
    * 7种文件类型：f b c d l s p
* -perm 775/-775
    * 后者包含前者，即-775包括775、776、777
* -exec find自带的执行工具
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/5idjPL4.png" alt="图片" style="zoom: 50%;" />
    * 统计已编写的c、cpp、sh代码行数
    * -exec—命令的开始
    * {} —— find的结果
    * \; —— 命令的结束
## ➕AWK 文本数据处理

```shell
awk [-Ffv] 'BEGIN { commands } pattern { commands } END { commands } file'
```
* 常用选项
    * -F fs：指定分隔符fs，可以是字符或正则表达式，默认为空格
    * -v var=value：传递外部变量var给awk
    * -f scriptfile：从脚本文件scriptfile读取awk命令
* BEGIN{}：开始执行一次
* pattern{}：重点⭐
    * awk读取的每一行都会执行一次
    * 如果没有提供pattern语句块，则默认执行{ print }
* END{}：最后执行一次
* [PS]
    * 可以从文件file读取数据，也可以用管道 "|" 传递数据
    * commands支持各种语言的print函数，如C语言的printf()
* 示例：统计最近登录的总时长
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/Q7M1tku.png" alt="图片" style="zoom: 67%;" />
    * 先预处理数据
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/eV8i8Uz.png" alt="图片" style="zoom:67%;" />
    * 使用awk取到需要的时长数据
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/DzWbc6I.png" alt="图片" style="zoom:67%;" />
    * 使用awk做计算
    * 常用内建变量：$n、NF、NR

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/ERq6VxT.png" alt="图片" style="zoom: 67%;" />

* [PS]
    * 参考文档——[AWK程序设计语言](https://awk.readthedocs.io/en/latest/)
    * 不用强背，知道它的功能即可
    * 其偏向于专业性；其实是一门语言，有自己的语法结构
    * 有很多变种

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/72FyNr2.png" alt="图片" style="zoom: 50%;" />

# 附加知识点

* 对于/var下的run目录：ls -al run/，ls -ald run/，ls -ald run
    * 三者的结果都不一样，ls -ald run也可以用ls -al run
    * 注意斜杠/的存在
* PATH变量是在进程里，断开SSH后重连，PATH变量会恢复原样
    * 严格说是在内存里
* 管道文件[mkfifo创建的] 和管道[命令中的 | ] 的区别
    * 本质上没有区别：一进一出，没输入就会产生阻塞
    * 但是，| 隐含创建了一个进程，处理了数据后再返回给原进程
* ⭐Linux中，文本处理的基本单位：行
    * cat、tac、scanf、printf
* 【硬连接】
    * 相当于一个文件多了一个别名，删除一个别名不影响文件
    * 对于存在硬连接的a、b文件
        * 通过ls -i 查看inode [文件结点号]
        * 会发现a、b两个文件的inode相同，且文件连接数为2
* chmod -R ...：递归，修改目录及下面所有文件的权限
* 解压压缩：gzip/gunzip、tar -c压缩/-x解压/-v显示冗余信息/-z通过gzip操作
    * tar不能解压.gz文件，使用gunzip即可
* ⭐创建的目录默认大小为4096 Byte = 4K
    * 对应磁盘的一个block，存储的是一个文件表
    * ❗目录大小不会为0，/proc下的大小为0的目录，属于虚拟文件系统，不是真的目录
# 思考点

* C的源文件被编译后的a.out，使用./a.out执行，这里的./是什么意思
    * 没有特殊含义，就是【当前路径】，用的是相对路径执行
    * 也可以使用绝对路径执行
    * ❓直接a.out呢?
        * a.out会被认为是命令，从而报错command not found
        * 对于默认的字符，系统默认为是执行命令，会找内置命令或去PATH路径下找
            * 默认为命令而不是文件，因为命令更常用
* 输出重定向的细节
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/kYOS7We.png" alt="图片" style="zoom:67%;" />
    * 方式①：会排队，按顺序输出
    * 方式②：同时传，数据会乱序
    * &1 = 1号文件
    * 此外，方式①还有顺序要求
        * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/0cWaeDz.png" alt="图片" style="zoom: 80%;" />
        * 这样，2号文件没有输出到1号文件里
        * 这里顺序有讲究，得先打开xxx.log文件！
* find找不到文件时，再ls容易让人产生错觉
    * <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/XzBxvwH.png" alt="图片" style="zoom: 50%;" />
    * 相当于ls 空，即ls当前目录
# Tips

* 【避免rm危险操作】
    * 写脚本定时[contab]将代码上传到Github、码云
    * 把rm封装改成mv，给自己定义的回收站，写脚本定时清理超过3天的文件
* 【小技巧】shell下，输入一小部分代码，按上下键可以匹配曾输过的匹配的代码
    * [其它] 使用叹号!开头
        * !mk 直接输入在历史记录里最近的匹配命令
        * !9812 直接输入history里第9812行
* 一般-r、-R表示递归，可自己尝试，小写被占用了就会用大写
* 聊到link默认指硬连接
* man手册搜索参数时，使用"/-z,"，在后面加一个逗号，匹配数更少
* $()可以充当命令替换符``

---


# 课程速记

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/pIfhHUC.png" alt="图片" style="zoom:67%;" />

* <img src="https://gitee.com/doubleL3/blog-imgs/raw/master/img/j4pjY4j.png" alt="图片" style="zoom:67%;" />