# Linux学习

## Vmware Linux虚拟机安装记录

参考：https://blog.csdn.net/kerwin_tang97/article/details/106174620



网关设置：ip需要同NAT在同一子网下

https://www.cnblogs.com/memebuguoshixingfu/p/12751620.html



通过ssh访问NAT网络模式下的虚拟机Linux:

https://blog.csdn.net/jiuduan2009/article/details/51737004



## Linux软链接和硬链接

硬链接： 与普通文件没什么不同，inode 都指向同一个文件在硬盘中的区块
软链接： 保存了其代表的文件的绝对路径，是另外一种文件，在硬盘上有独立的区块，访问时替换自身路径。

![image-20220327232846841](https://gitee.com/yu-xiong-yu/typora-img/raw/master/Img/202203290859621.png)

参考：https://www.linuxprobe.com/soft-and-hard-links.html



## Bash脚本

$$
Shell本身的PID（ProcessID）
$!
Shell最后运行的后台Process的PID
$?
最后运行的命令的结束代码（返回值）
$-
使用Set命令设定的Flag一览
$*
所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
$@
所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
$#
添加到Shell的参数个数
$0
Shell本身的文件名
${1}～${n}
添加到Shell的各参数值。$1是第1参数、$2是第2参数…。





## 外链学习

https://www.cnblogs.com/love-yh/p/8966438.html



# Linux命令

参考博客（较全）：https://blog.csdn.net/qq_23329167/article/details/83856430/



## 常用的Linux命令

*平时一定要多使用这些基础的命令！*

1）、cd : 改变目录。
2）、cd . . 回退到上一个目录，直接cd进入默认目录
3）、pwd : 显示当前所在的目录路径。
4）、ls(ll):  都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细。
5）、touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件。
6）、rm:  删除一个文件, rm index.js 就会把index.js文件删除。
7）、mkdir:  新建一个目录,就是新建一个文件夹。
8）、rm -rf :  删除一个文件夹, rm -r src 删除src目录 -f：不询问
rm -rf / 切勿在Linux中尝试！删除电脑中全部文件！
9）、mv 移动文件, mv index.html src index.html 是我们要移动的文件, src 是目标文件夹,当然, 这样写,必须保证文件和目标文件夹在同一目录下。
10）、reset 重新初始化终端/清屏。
11）、clear 清屏。
12）、history 查看命令历史。
13）、help 帮助。
14）、exit 退出。
15）、#表示注释

Reboot是重启，shutdown-s是关机，shutdown-r是重启，netstat是显示网络状态，half关机



基础命令

基础命令相关一：Cd、ls、pwd、help、man、if、for、while、case、select、read、test、ansible、iptables、firewall-cmd、salt、mv、cut、uniq、sort、wc、source、sestatus、setenforce;

基础命令相关二：Date、ntpdate、crontab、rsync、ssh、scp、nohup、sh、bash、hostname、hostnamectl、source、ulimit、export、env、set、at、dir、db_load、diff、dmsetup、declare;

用户权限相关：Useradd、userdel、usermod、groupadd、groupmod、groupdel、Chmod、chown、chgrp、umask、chattr、lsattr、id、who、whoami、last、su、sudo、w、chpasswd、chroot;

文件管理相关：Touch、mkdir、rm、rmdi、vi、vim、cat、head、tail、less、more、find、sed、grep、awk、echo、ln、stat、file;

软件资源管理：Rpm、yum、tar、unzip、zip、gzip、wget、curl、rz、sz、jar、apt-get、bzip2、service、systemctl、make、cmake、chkconfig;

系统资源管理：Fdisk、mount、umount、mkfs.ext4、fsck.ext4、parted、lvm、dd、du、df、top、iftop、free、w、uptime、iostat、vmstat、iotop、ps、netstat、lsof、ss、sar;

网络管理相关：Ping、ifconfig、ip addr、ifup、ifdown、nmcli、route、nslookup、traceroute、dig、tcpdump、nmap、brctl、ethtool、setup、arp、ab、iperf;

Linux系统开关机：Init、reboot、shutdown、halt、poweroff、runlevel、login、logout、exit。





## 杀死指定进程

查找指定进程格式：

```xml
ps -ef | grep 进程关键字
```

![image-20210915175143815](https://gitee.com/yu-xiong-yu/typora-img/raw/master/Img/image-20210915175143815.png)



## 开启防火墙命令

https://www.cnblogs.com/hebiao/p/12966000.html



## linux让程序在服务器一直运行

1 编写脚本文件，在指定目录下创建一个以 .sh 后缀的文件。我的是在 /usr/local/projects/myblog/  创建 myblog.sh 文件，执行命令 touch myblog.sh ，执行 vi myblog.sh 编辑内容如下：

```xml
# !/bin/sh
nohup java -jar myblog.war &
```

//如果脚本和项目不在同一个目录下，需要写目录
保存退出。

2 运行脚本
sh myblog.sh

3 想要结束进程的话，先查看进程 ps -ef | grep java
找到对应进程号 我这里是 4330 执行命令
kill -9 4330
下次要启动再次运行脚本即可。


注：
nohup写在命令前面
&写在命令后面
作用都是让程序后台运行

注意：因为日志是生成在 .sh 所在目录下的  nohup.out 文件，需注意时间久了日志文件过大

Linux命令后面加上 & 可以同时执行多个命令（跑多个项目）





## Linux下查看log常用的命令

https://www.jianshu.com/p/addfdb501e81



## [linux 中文件夹的文件按照时间倒序或者升序排列](https://www.cnblogs.com/wangkongming/p/3994962.html)

1，按照时间升序

```
命令:ls -lrt
详细解释:

-l     use a long listing format  以长列表方式显示（详细信息方式）
-t     sort by modification time 按修改时间排序（最新的在最前面）
-r     reverse order while sorting （反序）
```

2，按照时间降序（最新修改的排在前面）

```
命令:ls -lt
详细解释:

-l     use a long listing format  以长列表方式显示（详细信息方式）
-t     sort by modification time 按修改时间排序（最新的在最前面）
```



## ps命令详解

### 相关参数

1）ps a 显示现行终端机下的所有程序，包括其他用户的程序。

2）ps -A 显示所有程序。

3）ps c 列出程序时，显示每个程序真正的指令名称，而不包含路径，参数或常驻服务的标示。

4）ps -e 此参数的效果和指定"A"参数相同。

5）ps e 列出程序时，显示每个程序所使用的环境变量。

6）ps f 用ASCII字符显示树状结构，表达程序间的相互关系。

7）ps -H 显示树状结构，表示程序间的相互关系。

8）ps -N 显示所有的程序，除了执行ps指令终端机下的程序之外。

9）ps s 采用程序信号的格式显示程序状况。

10）ps S 列出程序时，包括已中断的子程序资料。

11）ps -t <终端机编号> 　指定终端机编号，并列出属于该终端机的程序的状况。

12）ps u 　 以用户为主的格式来显示程序状况。

13）ps x 　 显示所有程序，不以终端机来区分



示例：

```shell
# ps aux |more
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
polkitd   8683  0.0  0.0  39744  9536 pts/0    Ssl+ Mar24  13:25 /usr/local/bin/redis-server /etc/redis.conf
root      8793  0.0  0.0 140096 12288 pts/0    Ssl+ Mar24  15:16 redis-server *:6376
zhaojie   9926  0.0  0.0 110720  2496 pts/4    S+   16:59   0:00 grep --color=auto redis
zhaojie  46814  0.0  0.0 153664  7680 pts/0    Sl+  16:27   0:00 src/redis-server 0.0.0.0:6380

```



### ps -aux | grep 进程名： 进程pid一直变化

剔除掉grep本身产生的一条输出，在命令后面加上-v grep : `ps -aux | grep app | grep -v grep`

参考：https://blog.csdn.net/Nick_Zhang_CSDN/article/details/103343008
