考试重点：

![image-20240426174009415](C:\Users\37501\AppData\Roaming\Typora\typora-user-images\image-20240426174009415.png)



## 简答题

**linux系统的特点：**

1. 自由软件
2. 良好的兼容性
3. 良好的界面
4. 丰富的网络功能
5. 支持多种平台

**vi工作模式：**

1. 命令模式
2. 编辑模式
3. 命令项模式

**0号和1号进程**

0号进程和1号进程作为UNIX/LINUX系统中两个最重要的进程。

在UNIX系统中0号进程是唯一只在核心态下执行的进程。功能有三点：

1. 调度分配处理机
2. 负责进程交换
3. 初始化时创建1号进程

Linux中：0号进程在创建1号进程后变成空闲进程，当系统中没有其他进程就绪时它才运行，而它原来的功能被分散在几个内核进程中。



1号进程（init或systemd）是系统启动时创建的创建进程的进程，早期叫做init，现在叫做systemd。主要作用是：

1. 根据启动配置的文件的内容初始化系统、创建系统运行所需的进程。
2. 在linux中，还要根据运行级别触发运行级别初始化事件，执行相应的脚本程序进行初始化。
3. 在系统初始化完成后，1号进程变成了回收进程，专门领养没有父进程的孤儿进程或者回收状态为ZOMBIE的僵尸进程。

**Linux操作系统的基本安全制度：**

1. BIOS安全设置：密码
2. GRUB安全设置：密码
3. Linux系统用户账号：
4. Linux系统日志文件：
   + `/var/log/wtmp`：用户登录和退出日志      
   + `/var/run/utmp`：系统正在工作的用户信息，who命令使用
   + `/var/run/btmp`：企图登录系统但没有成功者的信息
   + `/var/log/lastlog`：最后一次进入linux系统的用户信息，有lastlog使用
   + ​		`/var/log/secure`：安全日志，系统认证信息
   + ​		`	/var/log/message`：系统日志，激励发生的各种事情
5. Linux文件系统的权限
6. 防火墙







## 第一章

主版本号.次版本号.修正号

次版本号为偶数的是稳定版，奇数为测试版



## 第二章

两种操作界面切换：图形在F1，字符在2-6

+ 图形向字符转换组合键Ctrl+Alt+Fm，
+ 字符界面向图形界面切换：Alt+Fm，当前在哪个就是哪个

系统的运行级别：

0：关闭系统

1、s、S：单用户或者系统维护模式

2：多用户模式

3：完全多用户模式

4：自定义的

5：完全多用户且有图形界面

6：重新启动

Q、q：重新加载配置文件

```bash
who -r    runlevel
systemctl get-default
init LEVEL
telinit LEVEL

init 0关机
init 6重启
```





man命令





## 第三章

文件类型：

+ 普通文件
+ 目录文件
+ 设备文件：每一个I/O设备都看成一个文件，跟普通文件一样处理，块设备，字符设备，符号链接，套接字，管道设备

目录结构

I/O重定向：输入0 输出1 错误2

```bash
>> 追击
&> 标准输出和标准错误同时重定向
&>> 
x >& 
```

管道： 

```bash
cat xxx | wc -l
```

Linux系统基本命令

less（better more）  

more

cat

wc

grep：-v反向搜索，-E正则表达式

echo：-e处理转义字符，默认不处理，-n不处理换行

## 第四章

**用户、组、密码管理**：

```bash
# 用户与uid：存放在/etc/passwd，可用id命令查询
# 组与gid：存放在/etc/group中，gid命令可以查询

## 增加用户 useradd
useradd [option] user
useradd -D  # 显示默认值
useradd -b base_dir
useradd -c comment
useradd -d home_dir
useradd -e expire_date
useradd -g group
useradd -G 多个组

useradd -m 创建用户时创建用户家目录，未指定则用/home/username代替

```

与用户和组管理相关的文件：

+ /etc/passwd：系统用户数据库文件，所有已注册用户的信息
+ /etc/shadow：影子密码文件，启用时存放系统内用户加密后的密码和登录控制信息
+ /etc/group: 组定义文件
+ /etc/default/useradd：创建用户时使用的默认值。
+ /etc/login.defs: 用户创建与密码管理相关的常量值，影子密码配套。
+ /etc/skel:





### 第五章

权限管理：

+ r：对文件或目录的查看权限
+ w：对文件或目录的写权限
+ x：对文件的执行权或对目录的进入权限

权限控制：

+ 用户主有读写执行，则rwx
+ 同组人有读和执行，则r-x
+ 不分配则为---
+ 顺序为主、组、其他人：rwxr-x---

权限的数字表示：

+ 转化成二进制即可，如rwxr-xr-x表示为：111 101 101即为：755

默认权限与mask：

root用户默认为022，普通用户为002，

**目录默认：**755

**用户默认：**644

```bash
chmod a+x file # 对所有人增加执行权限
chmod u+rwx,go+rx file # 为文件主增所有权限，为组和其他人增加读和执行权限
chmod -R o-rwx /tmp/file # 递归去除其他人对file的所有权限
chmod -R 777 file # 将file目录下的所有文件和各级子目录内容权限设置为777

chown root file
chown zhang *.c
chown -R hhhh file/
chown -R dd:bin file
chown -R :bin mydat
```

在UNIX/Linux系统中，用户级别和执行权力是相关的。但是有些工作，如修改/etc/passwd和/etc/shadow文件等，对于普通用户也是必需的，因为它要修改自己的密码，但是系统中这些文件对于普通用户是不允许有写操作的，于是出现了让普通用户通过某种机制行使超级用户权限的问题。

+ 设置用户ID权限：setuid/suid
+ 设置组ID权限：setgid/sgid

+ n当一个程序具有suid属性时，它执行时的uid（有效uid）将是该程序所有者的uid，记为euid，而执行者的uid称为真实uid，记为ruid。此时，程序的执行者具有程序所有者的权限。

+ n当一个程序具有sgid属性时，它执行时的gid（有效gid）将是该程序的gid，记为egid，而执行者的gid称为真实gid，记为rgid。此时，程序的执行者具有程序所有者所在组的权限。

+ nsuid和sgid属性只对二进制可执行文件有效，而对可执行的脚本文件无效。

```bash
suid sgid sticky位均占据的是x执行位
chmod u+s file rwx -> rws
chmod g+s file rws -> rws
chmod ug+s 同时设置suid跟sgid
chmod 4755 suid
chmod 2755 sgid
chmod 6755 同时设置

sticky表示该目录中的文件只能被他的所有者或超级用户删除，更名或移动
值为1000.
chmod +t dir 或者 file，
chmod 1755 dir或者file

当执行位有x时位st，无则为ST
```







**IDE：**一般最多装4块，分别是主驱动器上的主从硬盘和从驱动器上的主从硬盘。

**IDE硬盘设备的形式为：**`/dev/hdm[n]`

+ m代表驱动器号，`/dev/hda`,`/dev/hdb`为主控制器上的主硬盘和从盘，`/dev/hdc/`,`/dev/hdd`是控制器上的主、从硬盘。
+ n代表第m个硬盘上的分区号，如

**SCSI：**`/dev/sdm[n]`

+ m代表物理设备，n为物理设备上的分区
+ `/dev/sda1`:第一个scsi硬盘的第一个分区





**通用命令：**`fdisk`,`parted`,gpt还有`gdisk`

```bash
## 分区
fdisk # 命令用法信息
fidsk -l --unit=cylinders 显示所有硬盘的分区信息，以柱面为单位

parted set 1 boot on #将分区1设为boot并激活它

## 文件系统创建
mkfs -t vfat /dev/sdb #在移动磁盘上创建vfat文件系统
mkfs -t ext2 
# 在IDE硬盘上的/dev/hda10分区上创建NTFS分区
mkfs -t ntfs /dev/hda10

## 文件系统挂载
# 手动挂载
mount -t vfat /dev/sdb1 /mnt/usb #将u盘上的vfat格式的文件系统挂载在~
mount -r -t ext2 /dev/fd0 /mnt/fd # 只读方式

# 卸载
umount即可

# 自动挂载
1.创建安装点：
mkdir -p /mnt/wind
2.编辑/etc/fstab文件，增加一行
/dev/hda5 /mnt/wind vfat rw 0 0# dump是否备份，是否开机检查

# 打包压缩tar
-c 创建新文档
-t 显示备份文件的内容列表
-x 从档案中提取文件
-A 合并文档
-u 只备份归档文件的被修改的文件
-r 追加

tar -cvf /tmp/mytar files # 创建tar包
tar -tvf mytar # 显示tar包内容
tar -xf 提取所有文件
tar -rvf 追加

tar -cvfz 创建.tgz格式压缩包
tar -xvfz xxx 提取指定文件

tar -cvfj 创建bzip2压缩包
tar -tcfj sss.tbz2 查看内容
tar -xvfj 提取
```



## 第六章

**0号和1号进程**

0号进程和1号进程作为UNIX/LINUX系统中两个最重要的进程。

在UNIX系统中0号进程是唯一只在核心态下执行的进程。功能有三点：

1. 调度分配处理机
2. 负责进程交换
3. 初始化时创建1号进程

Linux中：0号进程在创建1号进程后变成空闲进程，当系统中没有其他进程就绪时它才运行，而它原来的功能被分散在几个内核进程中。



1号进程（init或systemd）是系统启动时创建的创建进程的进程，早期叫做init，现在叫做systemd。主要作用是：

1. 根据启动配置的文件的内容初始化系统、创建系统运行所需的进程。
2. 在linux中，还要根据运行级别触发运行级别初始化事件，执行相应的脚本程序进行初始化。
3. 在系统初始化完成后，1号进程变成了回收进程，专门领养没有父进程的孤儿进程或者回收状态为ZOMBIE的僵尸进程。



**systemctl、定时任务调度at、crontab：**

```bash
at 1200 < myjob # 12:00 执行myjob
at 12:10 -f myjob 

at>ls -l >/tmp/whoami.a 	#“at>”为提示符
at>date >>/tmp/whoami.a 	#继续输入命令
at>Ctrl_D 			#按^D结束作业输入
at><EOF> 	#按^D后，at显示此行，并覆盖^D所在的行

at -l 列出已提交的作业
at -d 23 删除左右，-l显示编号

crontab：
5 0 * * * $HOME/job >> /out 2>&1
30 3 * * 0 /sbin/init 6 每周日3：30 重启系统
0 22 * * 1-5 mail -s "sdsds"
30 0-23/2 * * * 偶数点的半点广播信息

crontab -e 编辑
crontab -l 查看
```



## 第七章

+ 引导分区：1024道之前
+ 根分区：主要工作分区
+ 交换区：动态扩充内存，可以不指定
+ 其他分区：不考

日志管理：

rsyslog日志系统





## 第八章

/dev/fdx 软驱设备

/dev/sdx SCSI硬盘设备

/dev/sdxy SCSI硬盘设备上的分区

/dev/hdx IDE硬盘设备，整个硬盘

/dev/hdxy IDE硬盘设备上的分区

/dev/tty# 终端设备

/dev/ttys 串口通信设备

/dev/console 系统主控台

/dev/lpx 并口设备，打印机

/dev/loop-control loopback设备的控制设备

/dev/loopx 用于安装映像文件，可自动生成



## 第九章

与网络相关的配置文件：

/etc/hosts ：名字解析使用

/etc/services : 服务和端口协议对应文件，或者成服务定义文件

/etc/resolv.conf：域名服务器定义文件

/etc/host.conf：规定如何解析主机

/etc/networks：定义子网

/etc/hostname：存放静态主机名



## 第十章

简答题











+ SRE：传统unix使用
+ BRE：iso posix-2支持，basic regular ex
+ ERE：同上，extended regular ex，但不是BRE超集，不兼容。**awk，egrep使用ERE**



#### 包含的内容

1. 字符集：在指定的位置上匹配的一个或多个字符
2. 计数：指定其前面的字符重复的次数，比如“*”
3. 位置字符：特殊字符集，用于标明位置，比如行首、行末等。
4. 具有特殊意义的字符
5. 普通字符



字符集：

+ 元字符

