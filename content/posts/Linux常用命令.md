---
title: "Linux常用命令"
date: 2023-08-10T22:53:37+08:00
draft: false
showToc: true # 显示目录
TocOpen: true # 自动展开目录
categories: ["Linux"]
tags: ["Linux"]
---

# 1. 帮助文档

很多命令或程序都会通过执行的参数来展示参考文档，如命令加上 -h 或者 --help。

## man

查看命令的参考文档

```bash
man <cmd>
```

## info

查看命令的参考文档

```bash
info <cmd>
```

# 2. 命令

## sh/bash

使用sh或bash执行脚本

-n 检查语法

-x 调试，打印执行过程语句

```bash
sh a.sh
bash a.sh
```

## ssh

登录远程机器

-p port 指定端口，默认端口22

-o stricthostkeychecking=no 首次登陆免输yes登录

-i file 使用密钥文件

```bash
ssh <user>@,ip>
ssh <user>@<ip>:<path>

# 登录远程机器直接执行命令
ssh <use>r@<ip> <cmd>
```

## ssh-keygen

创建ssh key

公钥：~/.ssh/id_rsa.pub

私钥：~/.ssh/id_rsa

-f ~/.ssh/id_rsa 指定密钥文件名

-C "comment" 设置注释

```bash
ssh-keygen -t rsa

# 将公钥发送给远程机器
ssh-copy-id <user>@<ip>
```

## exit

退出当前shell

```bash
exit
```

## type

查看命令是程序还是alias还是shell关键字

-a 列出所有

```bash
type <cmd>
```

## whatis

命令介绍

```bash
whatis <cmd>
```

## whereis

显示命令、用户手册、源代码的位置

-b 仅二进制文件

-s 仅源代码

```bash
whereis <cmd>
```

## which

查找命令的位置

```bash
which <cmd>
```

## alias

命令别名

```bash
# 设置命令别名
alias l='ls -l'

# 查看命令别名
alias l
```

## unalias

取消命令别名

```bash
unalias <name>
```

## declare

声明变量

-a 数组

-i 整型

-x 全局变量

-r 只读

```bash
declare <var>=<value>
```

## export

声明全局变量，在子进程也可用

```bash
export <var>=<value>
```

## nohup

后台运行命令，忽略挂起信号

标准输出默认为 nohup.out

```bash
nohup <cmd> &

# 重定向标准输出和异常输出
nohup <cmd> & >> a.log 2>&1
```

## xargs

将标准输入分隔，依次作为后面命令的参数执行

通常接在管道后面，将其标准输出作为标准输入

```bash
# 打印文件的每一行
cat a.txt | xargs echo

# 查看目录中所有文件的类型
ls | xargs file
```

## watch

定期执行命令并输出结果

-n x 指定间隔x秒

-d 高亮显示输出差异

-t 不显示标题

```bash
watch <cmd>
```

## history

查看历史命令

-c 删除当前shell命令记录

```bash
history

# 查看最近n条
history n
```

# 3. 系统

## env

查看环境变量

```bash
env
```

## uname

操作系统名

-a Linux内核版本

```bash
uname
```

## lsb_release

LSB版本信息

-a 操作系统信息

```bash
lsb_release
```

## hostname

机器名

```bash
hostname
```

## shutdown

关机，默认1分钟后

-h +n/20:00/now 设置时间

-r 重启

-c 取消

```bash
shutdown
```

## poweroff

关机

```bash
poweroff
```

## reboot

重启

```bash
reboot
```

## passwd

修改密码

```bash
# 修改当前用户密码
passwd

# 修改指定用户密码
passwd <user>
```

## service

系统服务

```bash
# 查看所有服务状态
service --status-all

# 查看服务状态
service <serv> status

# 启动服务
service <serv> start

# 关闭服务
service <serv> stop

# 重启服务
service <serv> restart
```

## ulimit

查看各系统选项的限额

```bash
# 查看所有选项
ulimit -a

# 设定某个限额
ulimit -<option> n

# 不限制core文件大小，用于生成core文件进行分析
ulimit -c unlimited
```

# 4. 工具

## sleep

等待一段时间

```bash
# 等待n秒
sleep n
```

## date

系统时间

+'format' 格式化时间

format：

* %Y 年
* %m 月
* %d 日
* %H 时
* %M 分
* %S 秒
* %s 时间戳

-s time 设置时间

-d time 显示指定时间

time：

* @1691660040 时间戳
* 2017-03-08 12:00:00 日期加时间
* 1 day ago
* 2 hour
* 3 minute

```bash
date

# 格式化时间
date +'%Y-%m-%d %H:%M:%S'

# 时间戳
date +%s
```

## cal

日历

```bash
cal
```

## bc

计算器

```bash
bc
```

## dos2unix

去掉Windows的格式符

```bash
dos2unix <file>
```

## md5sum

计算文件md5

```bash
md5sum <file>
```

# 5. 库文件

## ldd

查看动态库依赖的库

```bash
ldd lib.so
```

## readelf

显示elf文件的信息

-a 全部信息，等价于 -h -l -S -s -r -d -V -A -I

-h 头文件

-l 程序头

-S 节头

-s 符号表段的项

-r 可重定位段

-d 动态段

-V 版本段

-A CPU架构

-I 符号柱状图

```bash
readelf lib.so
readelf lib.a
readelf <exe>

# 查看执行文件所依赖的库
readelf -d exe | grep 'NEEDED'
```

## strace

跟踪系统调用

-e trace= 只跟踪指定调用，如open,close,rean,write,file,process等

-e signal= 指定系统信号

-o file 输出写到文件

-p pid 指定进程

-u user 指定用户

```bash
strace <exe>
```

# 6. 进程

## top

进程面板

-p pid 只显示该进程

-H 线程模式

内部指令：

* M 按内存排序
* P 按CPU排序
* T 按CPU时间排序
* c 显示命令/完整命令行
* i 忽略闲置和僵死进程
* 1 展示多cpu
* m 切换内存展示行
* s 更改刷新周期
* q 退出

```bash
top
# Mc 按内存排序且显示完整命令
```

## ps

进程列表

-ef 所有进程

-aux 所有进程，包括进程线程状态

-aL 查看轻量级进程

-l 详细信息

```bash
ps -ef
```

## pstree

打印进程树

-p pid 指定进程

```bash
# 所有进程的进程树
pstree

# 指定进程及其子进程
pstree -p 1234
```

## jobs

当前终端在后台运行的进程

-l 显示pid

```bash
jobs
```

## fg

将进程转为前台运行

```bash
fg <jobid>
```

## bg

将进程转为后台进行

```bash
bg <jobid>
```

## free

内存占用

-h 大小转为合适单位

```bash
free
```

## vmstat

查看虚拟内存

```bash
vmstat
```

## kill

向进程发送信号，默认信号15(SIGTERM，自行停止)

-n 指定信号

-l 列出信号

```bash
# 杀死进程
kill -9 <pid>

# 列出信号
kill -l
```

## killall

根据进程名杀死进程

```bash
killall <process>
```

## lsof

列出打开的文件

-p pid 指定进程

-i 符合条件的进程

+d dir 目录下当前一层被进程开启的文件

+D dir 目录下所有层被进程开启的文件

-u uid/user 该用户打开的文件

```bash
# 列出打开文件的进程
lsof <file>

# 列出进程打开的文件
lsof -p <pid>

# 列出所有打开的端口
lsof -i

# 列出端口被哪些进程占用
lsof -i:<port>
```

# 7. 网络

## netstat

查看网络连接

-t tcp连接

-u udp连接

-l 监听中的连接

-a 全部监听和未监听的连接

-n 展示数字形式的ip地址

-p 展示pid和程序名称

```bash
netstat -tunap
```

## ifconfig

查看网络接口

```bash
ifconfig
```

## ping

发送IP数据包，测试网络连通

```bash
ping <ip>
ping <hostname>
```

## traceroute

跟踪数据到达主机所经路由

```bash
traceroute <ip>
traceroute <hostname>
```

## nc

连接与监听TCP/UDP

```bash
nc <ip> <port>
```

## curl

下载URL的内容，默认输出到命令行。

\> file 内容保存到文件

-o file 内容保存到文件

-O 根据url中的文件名保存

```bash
curl <url>
```

## wget

下载URL的内容，默认保存到本地文件。

-q 不输出信息

-d 打印调试信息

-o file 输出信息保存到文件

-i file 从文件读取url

-a file 指定日志文件

```bash
wget <url>
```

# 8. 磁盘

## df

分区磁盘占用

-h 大小转为合适单位

```bash
df -h
```

## du

文件磁盘占用

-s 按文件夹汇总

-h 大小转为合适单位

```bash
du -sh <dir>
```

## mount

挂载文件系统

-a 按照/etc/fstab的配置挂载

-t type 指定文件系统

```bash
mount <设备> <挂载目录>

# 将cdrom驱动器挂载到目录上
mount /dev/hdc /mnt/cdrom
```

## umount

取消挂载

```bash
umount <设备/挂载目录>
```

## fdisk

磁盘分区工具

-l 列出各设备的分区表

```bash
fdisk <设备>
```

## mkfs

磁盘格式化

-t type 指定文件系统

```bash
mkfs <设备>
```

## fsck

磁盘检验

-t type 指定文件系统

-f 强制细化检验

-C 显示进度

```bash
fsck <设备>
```

## chmod

设置文件权限

文件权限为三位八进制数字，代表user/group/others的read/write/execute权限，如600表示user拥有read/write权限。

mode：

* 三位八进制数字，如644
* +/- r/w/x
* u/g/o +/- r/w/x

-R 递归设置目录

-f 强制

```bash
chmod <mode> <file>

# 给文件指定权限
chmod 644 a.log

# 给文件所有角色增加执行权限
chmod +x a.sh
chmod u+x a.sh
```

## chown

修改文件的所有者

-R 修改文件夹

```bash
chown <user> <file>
chown <group>:<user> <file>
```

## chgrp

修改文件的组

```bash
chown <group> <file>
```

## umask

新文件权限的补码

```bash
# 查险现在的补码
umask

# 设置补码
umask 022
```

## sync

内存数据写回到磁盘

```bash
sync
```

# 9. 用户

## who

显示有哪些正在登录计算机的用户

```bash
who
```

## whoami

显示当前用户的名字

```bash
whoami
```

## su

切换用户，需要root密码

```bash
# 切换为root
su

# 切换为root，并将目录切换至/root
su -

# 切换用户
su <user>
```

## sudo

使用root权限执行命令，需要当前用户密码

```bash
# 使用root权限
sudo <cmd>

# 切换用户为root
sudo su
```

## useradd

创建用户

```bash
useradd <user>
```

## userdel

删除用户

```bash
userdel <user>
```

## groupadd

创建组

```bash
groupadd <group>
```

## groupdel

删除组

```bash
groupdel <group>
```

## usermod

修改用户账户

-u uid 更改用户ID

-d dir 更改登陆目录

-s shell 更改登录的shell

```bash
# 把用户加入组
sudo usermod -aG group name
```

# 10. 目录

## ls

显示目录内容

-a 显示隐藏文件

-l 显示详细信息

-r 逆序展示

-t 按时间排序

-S 按大小排序

-i 显示inode号

-h 大小转为合适单位

-f 取消排序，同时会应用-a，当文件夹文件数量非常大时默认排序会很慢，可以用这个选项

--full-time 完整时间格式

```bash
# 显示当前目录
ls

# 显示指定目录或文件
ls <dir>
ls <file>

# 显示当前目录，按修改时间从旧到新排序
ls -lrt
```

## cd

进入目录

```bash
# 进入$HOME目录
cd

# 进入指定目录
cd <dir>

# 回到前一个目录
cd -
```

## pwd

显示当前目录绝对路径

```bash
pwd
```

## dirs

显示目录栈，通过命令切换目录并将目录加入目录栈，以及移除目录栈内容，来记住之前的目录。

```bash
# 显示目录栈
dirs

# 目录加入目录栈，切换目录
pushd <dir>

# 移除目录栈顶，切换目录
popd
```

## touch

创建空文件，如果已有文件则会更新时间

```bash
touch <file>
```

## mkdir

创建目录

-m MODE 指定权限

-p 如果某一层目录不存在则创建，直到最底层的子目录

```bash
mkdir <dir>
```

## rmdir

删除空目录

-p 同时删除空的父目录

-r 删除非空目录

```bash
rmdir <dir>
```

# 11. 文件

## cp

复制文件

-f 强制

-r 复制目录

-p 复制权限和修改时间

-i 覆盖已有文件时提示

```bash
cp <file1> <file2>
```

## mv

移动文件，重命名。

-f 强制

-i 覆盖已有文件时提示

```bash
mv <file1> <file2>
```

## rm

删除文件

-f 强制

-r 删除目录

```bash
rm <file>
```

## ln

创建文件链接，默认为硬链接（同文件索引节点）

-s 软连接（索引文件）

```bash
# 创建硬链接
ln <oldfile> <newfile>

# 创建软链接
ln -s <oldfile> <newfile>
```

## wc

统计文件的行数、单词数、字节数

-l 行数

-w 单词数

-c 字节数

```bash
wc <file>
```

## file

文件类型

-f file 对文件每一行看作文件名查看文件类型

```bash
file <file>
```

## stat

查看文件状态，包括大小、inode信息、权限、创建更改访问时间。

```bash
stat <file>
```

## find

搜索目录的所有文件

! 取反

-a 且

-o 或

-name file 文件名

-mtime n/+n/-n 改动时间等于/大于/小于n天

-atime n 访问时间

```bash
# 列出目录下所有文件
find <dir>

# 按文件名搜索文件，支持通配符
find <dir> -name *.log

# 搜索1天内修改的文件
find <dir> -mtime -1
```

## locate

通过系统索引寻找文件的位置

```bash
locate <file>
```

## updatedb

更新文件位置系统索引

```bash
updatedb
```

## scp

在不同机器间复制文件，其中一个是本机器则不用写用户名和IP。

-r 递归目录

```bash
scp <user>@<ip>:<file> <user>@<ip>:<file>
```

## sz

从服务器传文件到本地

-a 文本

-be 二进制

-y 覆盖已有文件

```bash
sz <file>
```

## rz

从本地传文件到服务器

-b 二进制

-y 覆盖已有文件

```bash
rz
```

## ar

文件归档

-v 显示执行时的信息

```bash
# 将文件放入存档
ar -r <bak> <file>

# 将存档的文件取出
ar -x <bak>

# 显示存档的文件
ar -t <bak>

# 删除存档的某文件
ar -d <bak> <file>

# 编译静态链接库
ar cr liba.a a.o
```

## tar

压缩工具

```bash
# 压缩文件
tar -zcf a.tgz a

# 解压文件
tar -zxf a.tgz

# 归档文件
tar -cf a.tar a

# 恢复归档
tar -xf a.tar
```

## zip

压缩工具

-r 压缩目录

-q 不显示执行过程

-P passwd 设置密码

-m 压缩后删除源文件

```bash
zip a.zip <file>
```

## unzip

解压工具

-q 不显示执行过程

-P passwd 解压密码

-d dir 解压到指定目录

```bash
unzip a.zip
```

## gzip

压缩工具

-d 解压

-c 输出到标准输出

-r 压缩目录

```bash
gzip <file>
```

# 12. 文本打印

## cat

查看文件内容并输出

-n 加行号

-E/-e 每行末加'$'

-t 制表符^I，换页符^L

-v 显示非打印字符

```bash
cat <file>
```

## tac

按行倒序查看文件内容并输出

```bash
tac <file>
```

## strings

输出文件的可打印字符，如用于二进制文件，将可打印的内容展示出来。

```bash
strings <file>
```

## more

每次一屏查看文件，按空格下一屏，按回车下一行，按q退出。

-n 每次n行

+n 从第n行开始

```bash
more <file>
```

## less

查看文件，可以通过方向键或者jk向前向后看。

```bash
less <file>
```

## head

显示文件开始几行，默认10行。

-n 前n行

```bash
head <file>
```

## tail

显示文件最后几行，默认10行。

-n 后n行

-f 不退出，随着文件追加继续打印，一般用于查看正在打印的日志文件。等同于命令 tailf。

```bash
tail <file>

# 查看文件实时追加的内容
tail -f a.log
```

## echo

打印文本

-e 允许转义

\c 去掉换行符

```bash
echo <text>
```

## od

输出文件内容的八进制格式

-c 打印字符

-t d1 打印ASCII码

```bash
od <file>
```

# 13. 文本处理

## grep

文件内容匹配

-c 打印匹配行数

-i 忽略大小写

-n 打印行号

-v 输出非匹配行

-E 增强正则表达式，相当于egrep

-o 打印匹配的串

-l 只打印文件名

-w 打印匹配整行的

-r 递归目录查找

--line-buffered 缓冲输出

-A n 额外打印每处匹配的后n行

-B n 额外打印每处匹配的前n行

-C n 额外打印每处匹配的前后n行

```bash
grep <regex> <file>
```

## sed

文本处理

command：

* s替换
  * 's/aa/bb/' 文本替换，每行只替换第一次
  * 's/aa/bb/g' 文本替换，每行多次
* d删除
  * '/aa/d' 匹配并删除
  * '1,3d' 删除行
* a新增
  * '2,3a text' 每行的下一行插入文本
* c取代
  * '2,3c text' 这几行取代为文本
* p打印
  * -n '2,3p' 打印行
* i插入
  * '2i text' 每行前插入文本

-i 修改后的内容保存到文件

-r 扩展regex

-n 安静模式，只有p有输出

```bash
sed '<command>' <file>

# 将每个aa替换为bb
sed 's/aa/bb/g' a.log
```

## awk

文本处理

command：

* '/aa/' 匹配行
* '{print $1}' 打印某一域，$0为全行，$NF为最后一域
* {} 每行执行
* BEGIN{} 开始执行
* END{} 结束执行

变量：

* NR 当前记录数，每行递增
* FNR 当前记录数，每行递增，重新打开文件置1
* NF 本行字段数
* FS 分隔字符

-F 'sep' 指定分割域的串

-f file 从文件读取命令

-v var=x 定义awk内的变量，用数字、字符串、shell变量赋值，使用时直接用var

```bash
awk '<command>' <file>

# 以-为分隔符打印每行的第2、3段内容
awk -F"-" '{print $2,$3}' a.log

# 合并文件列
# file1:
#1	13
#2	423
#3	55
# file2:
#1	a
#2	b
awk 'NR==FNR{a[$1]=$2}NR>FNR{if(a[$1]!="")print $1,a[$1],$2}' file1 file2
```

## cut

剪切每行字符并输出

-b n 每行第n个字节

-c n 每行第n个字符

-d sep 指定字段分割字符，默认为tab

-f n 输出第n段

-f m,n 输出第m和第n段

-f m-n 输出第m至n段

```bash
# 输出以-为分隔符每行第二段内容
cut -d '-' -f 2 a.log
```

## paste

水平连接多个文件

```bash
paste <files>
```

## sort

文件每行排序

-t '' 设置分隔符，默认为空格制表符

-k n 按照第n个域排序

-r 反序

-n 数字排序

```bash
sort <file>
```

## uniq

删除连续的重复行

-c 打印连续次数

-d 只输出重复的

-u 只输出不重复的

```bash
uniq <file>

# 文件并集
cat file1 file2 | sort | uniq

# 文件交集
sort file1 | uniq > f1; sort file2 | uniq > f2; cat f1 f2 | sort | uniq -d
```

## diff

文件比较

```bash
diff <file1> <file2>

# 打开vim比较文件
vimdiff <file1> <file2>
```

