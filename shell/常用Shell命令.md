# 文件操作

## ls

功能：显示文件和目录的信息

ls　以默认方式显示当前目录文件列表

ls -a 显示所有文件包括隐藏文件

ls -l 显示文件属性，包括大小，日期，符号连接，是否可读写及是否可执行

ls -lh 显示文件的大小，以容易理解的格式印出文件大小 (例如 1K 234M2G)

ls -lt 显示文件，按照修改时间排序


## cd

功能：改名目录

cd dir　切换到当前目录下的dir目录

cd /　切换到根目录

cd ..　切换到到上一级目录

cd ../..　切换到上二级目录

cd ~　切换到用户目录，比如是root用户，则切换到/root下

## cp

功能：copy文件

cp source target　将文件source复制为target

cp /root /source.　将/root下的文件source复制到当前目录

cp –av soure_dir target_dir　将整个目录复制，两目录完全一样


## rm

功能：删除文件或目录

rm file　删除某一个文件

rm -f file 删除时候不进行提示。可以于r参数配合使用

rm -rf dir　删除当前目录下叫dir的整个目录


## mv

功能：将文件移动走，或者改名，在uinx下面没有改名的命令，如果想改名，可以使用该命令

mv source target　将文件source更名为target

## diff

功能：比较文件内容

diff dir1 dir2　比较目录1与目录2的文件列表是否相同，但不比较文件的实际内容，不同则列出

diff file1
file2　比较文件1与文件2的内容是否相同，如果是文本格式的文件，则将不相同的内容显示，如果是二进制代码则只表示两个文件是不同的

comm file1 file2　比较文件，显示两个文件不相同的内容


# 查看文件内容

## cat

显示文件的内容，和DOS的type相同

cat file

## tail

功能：显示文件的最后几行

tail -n 100 aaa.txt 显示文件aaa.txt文件的最后100行

## vi

vi file　编辑文件file

vi 原基本使用及命令：

输入命令的方式为先按[ESC]键，然后输入:w(写入文件),:w!(不询问方式写入文件）,:wq保存并退出,:q退出,q!不保存退出

## touch

功能：创建一个空文件

touch aaa.txt 创建一个空文件，文件名为aaa.txt


# 系统命令

## man

功能：查看某个命令的帮助，如果你不知道某个命令的用法不懂，可以问他，他知道就回告诉你

例如：

man ls 显示ls命令的帮助内容

## w

功能：显示登录用户的详细信息

## who

功能：显示登录用户

## last

功能：查看最近那些用户登录系统

## date
功能：系统日期设定

date　显示当前日期时间

date -s 20:30:30　设置系统时间为20:30:30

date -s 2002-3-5　设置系统时期为2003-3-5

date -s “060520 06:00:00”　设置系统时期为2006年5月20日6点整。

## clock

功能：时钟设置

clock –r　对系统Bios中读取时间参数

clock –w　将系统时间(如由date设置的时间)写入Bios


## uname

功能：查看系统版本

uname -R　显示操作系统内核的version

例如：

Sarge:~# uname -a

Linux Sarge 2.6.8-2-386 #1 Tue Aug 16 12:46:35 UTC 2005 i686 GNU/Linux

## 关闭重启计算机

reboot　 重新启动计算机

shutdown -r now 重新启动计算机，停止服务后重新启动计算机

shutdown -h now 关闭计算机，停止服务后再关闭系统

halt 关闭计算机

一般用shutdown -r now,在重启系统是，关闭相关服务，shutdown -h now也是如此。

## kill

功能：干掉某个进程，进程号可以通过ps命令得到

kill -9 1001　将进程编号为1001的程序干掉

kill all -9 apache　将所有名字为apapche的程序杀死，kill不是万能的，对僵死的程序则无效。

## ps

功能：显示进程信息

ps ux 显示当前用户的进程

ps uxwww 显示当前用户的进程的详细信息

ps aux 显示所有用户的进程

ps ef 显示系统所有进程信息

## top

功能：查看系统cpu、内存等使用情况


# 用户相关命令

## chmod

功能：改变用户的权限

chmod a+x file　将file文件设置为可执行，脚本类文件一定要这样设置一个，否则得用bash file才能执行

chmod 777 file　同上

chmod 666 file　将文件file设置为可读写

chmod 750 file 将文件file设置为，所有者为完全权限，同组可以读和执行，其他无权限


## id

功能：显示用户的信息，包括uid、gid等


## finger

功能：显示用的信息

# 压缩命令

## gzip

功能：压缩文件，gz格式的

注意：生成的文件会把源文件覆盖

gzip -v 压缩文件，并且显示进度

-d 解压缩

gnuzip -f 解压缩

例如：

Sarge:~# gzip a.sh

Sarge:~#ll

-rwxr-xr-x 1 root root 71 12月 18 21:08 a.sh.gz

Sarge:~# gzip -d a.sh.gz

Sarge:~#ll

-rwxr-xr-x 1 root root 48 12月 18 21:08 a.sh


## zip

功能：压缩和解压缩zip命令

zip

unzip

例如：

Sarge:~# zip a.sh.zip a.sh

adding: a.sh (stored 0%)

Sarge:~# ll

-rw-r–r– 1 root root 188 5月 21 10:37 a.sh.zip

Sarge:~# unzip a.sh.zip

Archive: a.sh.zip

replace a.sh? [y]es, [n]o, [A]ll, [N]one, [r]ename: r

new name: a1.sh

extracting: a1.sh

Sarge:~ # ll

-rwxr-xr-x 1 root root 48 12月 18 21:08 a1.sh


# 网络相关命令

## ifconfig

功能：显示修改网卡的信息

ifconfig 显示网络信息

ifconfig eth0 显示eth0网络信息

修改网络信息：

ifconfig eth0 192.168.1.1 netmask 255.255.255.0
设置网卡1的地址192.168.1.1，掩码为255.255.255.0

ifconfig eth0:1 192.168.1.2　 捆绑网卡1的第二个地址为192.168.1.2

ifconfig eth0:x 192.168.1.n　 捆绑网卡1的第n个地址为192.168.1.n

## route

功能：显示当前路由设置情况

route 显示当前路由设置情况，比较慢一般不用。

route add -net 10.0.0.0 netmask 255.255.0.0 gw 192.168.1.254 添加静态路由

route del -net 10.0.0.0 netmask 255.255.0.0 gw 192.168.1.254 添加静态路由

route add default gw 192.168.1.1 metric1　 设置192.168.1.1为默认的路由

route del default　 将默认的路由删除


## netstat

功能：显示网络状态

netstat -an 查看网络端口信息

netstat -nr 查看路由表信息，比route快多了，

## ping

ping



