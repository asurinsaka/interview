## Linux系统简单面试题

[zhihu](https://zhuanlan.zhihu.com/p/32250942)

### 如何看当前Linux系统有几颗物理CPU和每颗CPU的核数？
```bash
[root@centos6 ~ 10:55 #35]# cat /proc/cpuinfo|grep -c 'physical id'
4
[root@centos6 ~ 10:56 #36]# cat /proc/cpuinfo|grep -c 'processor'
4
```

### 查看系统负载有两个常用的命令，是哪两个？这三个数值表示什么含义呢？

```bash
[root@centos6 ~ 10:56 #37]# w
10:57:38 up 14 min, 1 user, load average: 0.00, 0.00, 0.00
USER TTY FROM LOGIN@ IDLE JCPU PCPU WHAT
root pts/0 192.168.147.1 18:44 0.00s 0.10s 0.00s w
[root@centos6 ~ 10:57 #38]# uptime
10:57:47 up 14 min, 1 user, load average: 0.00, 0.00, 0.00
```

Also sar
```bash
root@a371b9f94d28:/# sar 1 3
Linux 4.18.0-1-amd64 (a371b9f94d28)     10/25/18        _x86_64_        (8 CPU)

16:31:43        CPU     %user     %nice   %system   %iowait    %steal     %idle
16:31:44        all     16.30      0.00      9.51      0.00      0.00     74.20
16:31:45        all     16.05      0.00      9.63      0.12      0.00     74.20
16:31:46        all     16.29      0.00      9.08      0.37      0.00     74.25
Average:        all     16.21      0.00      9.41      0.17      0.00     74.22
```

```bash
%user
       Percentage of CPU utilization that occurred while executing at the user level (application). Note that this field includes time spent running virtual processors.
%nice
       Percentage of CPU utilization that occurred while executing at the user level with nice priority.
%system
       Percentage of CPU utilization that occurred while executing at the system level (kernel). Note that this field includes time spent servicing hardware and software interrupts.
%iowait
       Percentage of time that the CPU or CPUs were idle during which the system had an outstanding disk I/O request.
%steal
       Percentage of time spent in involuntary wait by the virtual CPU or CPUs while the hypervisor was servicing another virtual processor.
%idle
       Percentage of time that the CPU or CPUs were idle and the system did not have an outstanding disk I/O request.
```


Also top
```bash
(09:52 asurin@DESKTOP-2EDCEVS network) > top

top - 09:52:57 up 7 days, 21:47, 17 users,  load average: 1.46, 2.65, 2.37
Tasks: 490 total,   3 running, 484 sleeping,   0 stopped,   3 zombie
%Cpu(s):  9.5 us,  2.9 sy,  0.0 ni, 86.4 id,  0.0 wa,  0.0 hi,  1.2 si,  0.0 st
MiB Mem :  15981.7 total,    176.4 free,  14858.5 used,    946.8 buff/cache
MiB Swap:  35402.0 total,  21636.8 free,  13765.2 used.    578.4 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                          
 1882 root      20   0  325328  56152  12764 R  15.2   0.3 628:09.82 Xorg                                             
 2029 asurin    20   0 4846392 594148  55492 S  14.2   3.6 335:52.65 gnome-shell                                      
 3307 asurin    20   0  483212  13208   3552 R  10.6   0.1 460:38.13 gnome-system-mo                                  
 3466 asurin    20   0 3324124 364000  59096 S   5.6   2.2 664:18.32 chrome           
 ```
 Load average
 ```bash
  system load avg over the last 1, 5 and 15 minutes
  ```

 CPU Stat
 ```bash
 us, user    : time running un-niced user processes
 sy, system  : time running kernel processes
 ni, nice    : time running niced user processes
 id, idle    : time spent in the kernel idle handler
 wa, IO-wait : time waiting for I/O completion
 hi : time spent servicing hardware interrupts
 si : time spent servicing software interrupts
 st : time stolen from this vm by the hypervisor
```

### vmstat r, b, si, so, bi, bo 这几列表示什么含义呢？
```bash
[root@centos6 ~ 10:57 #39]# vmstat
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
r b swpd free buff cache si so bi bo in cs us sy id wa st
0 0 0 1783964 13172 106056 0 0 29 7 15 11 0 0 99 0 0
```

FIELD DESCRIPTION FOR VM
```bash
   Procs
       r: The number of runnable processes (running or waiting for run time).
       b: The number of processes in uninterruptible sleep.

   Memory
       swpd: the amount of virtual memory used.
       free: the amount of idle memory.
       buff: the amount of memory used as buffers.
       cache: the amount of memory used as cache.
       inact: the amount of inactive memory.  (-a option)
       active: the amount of active memory.  (-a option)

   Swap
       si: Amount of memory swapped in from disk (/s).
       so: Amount of memory swapped to disk (/s).

   IO
       bi: Blocks received from a block device (blocks/s).
       bo: Blocks sent to a block device (blocks/s).

   System
       in: The number of interrupts per second, including the clock.
       cs: The number of context switches per second.

   CPU
       These are percentages of total CPU time.
       us: Time spent running non-kernel code.  (user time, including nice time)
       sy: Time spent running kernel code.  (system time)
       id: Time spent idle.  Prior to Linux 2.5.41, this includes IO-wait time.
       wa: Time spent waiting for IO.  Prior to Linux 2.5.41, included in idle.
       st: Time stolen from a virtual machine.  Prior to Linux 2.6.11, unknown.
```

### linux系统里，您知道buffer和cache如何区分吗？

buffer和cache都是内存中的一块区域，当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer，然后CPU去执行其他任务，buffer中的数据会定期写入磁盘；当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多。

### 使用top查看系统资源占用情况时，哪一列表示内存占用呢？

```bash
PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
301 root      20   0     0    0    0 S  0.3  0.0   0:00.08 jbd2/sda3-8
1 root      20   0  2900 1428 1216 S  0.0  0.1   0:01.28 init
2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd
3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0
```
* VIRT虚拟内存用量
* RES物理内存用量
* SHR共享内存用量
* %MEM内存用量

### 如何实时查看网卡流量为多少？如何查看历史网卡流量？
nload
iftop

安装sysstat包，使用sar命令查看。
```bash
yum install -y sysstat#安装sysstat包，获得sar命令
sar -n DEV#查看网卡流量，默认10分钟更新一次
sar -n DEV 1 10#一秒显示一次，一共显示10次
sar -n DEV -f /var/log/sa/sa22#查看指定日期的流量日志
```

### 如何查看当前系统都有哪些进程？
ps aux  # bsd
```bash
root@a371b9f94d28:/# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND                                             
root         1  0.0  0.0  18504  1568 pts/0    Ss   Oct18   0:00 /bin/bash                                           
root       734  0.0  0.0  34400  2660 pts/0    R+   15:16   0:00 ps aux
```
ps -elf  # standard
```bash
[root@centos6 ~ 13:21 #57]# ps -elf
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root         1     0  0  80   0 -   725 -      10:43 ?        00:00:01 /sbin/init
1 S root         2     0  0  80   0 -     0 -      10:43 ?        00:00:00 [kthreadd]
1 S root         3     2  0 -40   - -     0 -      10:43 ?        00:00:00 [migration/0]
1 S root         4     2  0  80   0 -     0 -      10:43 ?        00:00:00 [ksoftirqd/0]
1 S root         5     2  0 -40   - -     0 -      10:43 ?        00:00:00 [migration/0]
```
### ps 查看系统进程时，有一列为STAT， 如果当前进程的stat为Ss 表示什么含义？如果为Z表示什么含义？

答：S表示正在休眠；s表示主进程；Z表示僵尸进程。
```bash
PROCESS STATE CODES
       Here are the different values that the s, stat and state output specifiers (header "STAT" or "S") will
       display to describe the state of a process:

               D    uninterruptible sleep (usually IO)
               I    Idle kernel thread
               R    running or runnable (on run queue)
               S    interruptible sleep (waiting for an event to complete)
               T    stopped by job control signal
               t    stopped by debugger during the tracing
               W    paging (not valid since the 2.6.xx kernel)
               X    dead (should never be seen)
               Z    defunct ("zombie") process, terminated but not reaped by its parent

       For BSD formats and when the stat keyword is used, additional characters may be displayed:

               <    high-priority (not nice to other users)
               N    low-priority (nice to other users)
               L    has pages locked into memory (for real-time and custom IO)
               s    is a session leader
               l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
               +    is in the foreground process group
```

### 如何查看系统都开启了哪些端口？
```bash
[root@centos6 ~ 13:20 #55]# netstat -lnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      1035/sshd
tcp        0      0 :::22                       :::*                        LISTEN      1035/sshd
udp        0      0 0.0.0.0:68                  0.0.0.0:*                               931/dhclient
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node PID/Program name    Path
unix  2      [ ACC ]     STREAM     LISTENING     6825   1/init              @/com/ubuntu/upstart
unix  2      [ ACC ]     STREAM     LISTENING     8429   1003/dbus-daemon    /var/run/dbus/system_bus_socket
```
### 如何查看网络连接状况？
```bash
[root@centos6 ~ 13:22 #58]# netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN
tcp        0      0 192.168.147.130:22          192.168.147.1:23893         ESTABLISHED
tcp        0      0 :::22                       :::*                        LISTEN
udp        0      0 0.0.0.0:68                  0.0.0.0:*
……
```

ifconfig
ip a s

### 想修改ip，需要编辑哪个配置文件，修改完配置文件后，如何重启网卡，使配置生效？

答：使用vi或者vim编辑器编辑网卡配置文件/etc/sysconfig/network-scripts/ifcft-eth0（如果是eth1文件名为ifcft-eth1），内容如下

```bash
DEVICE=eth0
HWADDR=00:0C:29:06:37:BA
TYPE=Ethernet
UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=192.168.147.130
NETMASK=255.255.255.0
GATEWAY=192.168.147.2
DNS1=192.168.147.2
DNS2=8.8.8.8
```

修改网卡后，可以使用命令重启网卡：
```bash
ifdown eth0
ifup eth0
ip link set eth0 down
ip link set eth0 up
```

也可以重启网络服务：
```bash
systemctl restart networking
```

### 能否给一个网卡配置多个IP? 如果能，怎么配置？

答：可以给一个网卡配置多个IP，配置步骤如下：
```
cat /etc/sysconfig/network-scripts/ifcfg-eth0#查看eth0的配置
DEVICE=eth0
HWADDR=00:0C:29:06:37:BA
TYPE=Ethernet
UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=192.168.147.130
NETMASK=255.255.255.0
GATEWAY=192.168.147.2
DNS1=192.168.147.2
DNS2=8.8.8.8
```
1. 新建一个ifcfg-eth0:1文件

`cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:1`

2. 修改其内容如下：
```bash
vim /etc/sysconfig/network-scripts/ifcfg-eth0:1
DEVICE=eth0:1
HWADDR=00:0C:29:06:37:BA
TYPE=Ethernet
UUID=0eea1820-1fe8-4a80-a6f0-39b3d314f8da
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=192.168.147.133
NETMASK=255.255.255.0
GATEWAY=192.168.147.2
DNS1=192.168.147.2
DNS2=8.8.8.8
```
3. 重启网络服务：

`systemctl restart networking

### 如何查看某个网卡是否连接着交换机？

答：mii-tool eth0 或者 mii-tool eth1

### 如何查看当前主机的主机名，如何修改主机名？要想重启后依旧生效，需要修改哪个配 置文件呢？
答：查看主机名：
```bash
hostname
centos6.5
```
修改主机名：

```bash
hostname centos6.5-1
hostnamectl set-hostname [hostname]
```
永久生效需要修改配置文件：

`/etc/hostname`

### 设置DNS需要修改哪个配置文件？
1. 在文件 /etc/resolv.conf 中设置DNS
2. 在文件 /etc/sysconfig/network-scripts/ifcfg-eth0 中设置DNS

### 如何备份某个用户的任务计划？
将/var/spool/cron/目录下指定用户的任务计划拷贝到备份目录cron_bak/下即可

### 任务计划格式中，前面5个数字分表表示什么含义？

答：依次表示：分、时、日、月、周

### 如何可以把系统中不用的服务关掉？

```bash
systemctl list-units --type=service
systemctl stop [service_name]
systemctl disable [service_name]
```

### systemd 的简介和特点
Systemd 是 Linux 系统中最新的初始化系统（init），它主要的设计目标是克服 sysvinit 固有的缺点，提高系统的启动速度。
Systemd 提供了比 UpStart 更激进的并行启动能力，采用了 socket / D-Bus activation 等技术启动服务。一个显而易见的结果就是：更快的启动速度。

为了减少系统启动时间，systemd 的目标是：

尽可能启动更少的进程
尽可能将更多进程并行启动

[IBM developerworks](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html)

### 有一个脚本运行时间可能超过2天，如何做才能使其不间断的运行，而且还可以随时观察脚本运行时的输出信息？
答：使用screen工具

### 在Linux系统下如何按照下面要求抓包：只过滤出访问http服务的，目标ip为192.168.0.111，一共抓1000个包，并且保存到1.cap文件中？
```bash
tcpdump -nn -s0 host 192.168.0.111 and port 80 -c 1000 -w 1.cap
```

### 解析域名
```bash
dig
nslookup
```

### 自定义解析域名的时候，我们可以编辑哪个文件？是否可以一个ip对应多个域名？是否一个域名对应多个ip？
答：编辑 /etc/hosts ,可以一个ip对应多个域名，不可以一个域名对多个ip

### 我们可以使用哪个命令查看系统的历史负载（比如说两天前的）？

`sar -q -f /var/log/sa/sa22  #查看22号的系统负载`

### 在Linux下如何指定dns服务器，来解析某个域名？

答：使用dig命令：dig @DNSip http://domain.com

如：

`dig @8.8.8.8 www.baidu.com#使用谷歌DNS解析百度`

### 有一天你突然发现公司网站访问速度变的很慢很慢，你该怎么办呢？

答：可以从两个方面入手分析：分析系统负载，使用w命令或者uptime命令查看系统负载，如果负载很高，则使用top命令查看CPU，MEM等占用情况，要么是CPU繁忙，要么是内存不够，如果这二者都正常，再去使用sar命令分析网卡流量，分析是不是遭到了攻击。一旦分析出问题的原因，采取对应的措施解决，如决定要不要杀死一些进程，或者禁止一些访问等。

### Individual Block Device I/O Activities (sar -d)
```bash
$ sar -d 1 1
Linux 2.6.18-194.el5PAE (dev-db)        03/26/2011      _i686_  (8 CPU)

01:59:45 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
01:59:46 PM    dev8-0      1.01      0.00      0.00      0.00      0.00      4.00      1.00      0.10
01:59:46 PM    dev8-1      1.01      0.00      0.00      0.00      0.00      4.00      1.00      0.10
01:59:46 PM dev120-64      3.03     64.65      0.00     21.33      0.03      9.33      5.33      1.62
01:59:46 PM dev120-65      3.03     64.65      0.00     21.33      0.03      9.33      5.33      1.62
01:59:46 PM  dev120-0      8.08      0.00    105.05     13.00      0.00      0.38      0.38      0.30
01:59:46 PM  dev120-1      8.08      0.00    105.05     13.00      0.00      0.38      0.38      0.30
01:59:46 PM dev120-96      1.01      8.08      0.00      8.00      0.01      9.00      9.00      0.91
01:59:46 PM dev120-97      1.01      8.08      0.00      8.00      0.01      9.00      9.00      0.91
$ sar -p -d 1 1
Linux 2.6.18-194.el5PAE (dev-db)        03/26/2011      _i686_  (8 CPU)

01:59:45 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
01:59:46 PM       sda      1.01      0.00      0.00      0.00      0.00      4.00      1.00      0.10
01:59:46 PM      sda1      1.01      0.00      0.00      0.00      0.00      4.00      1.00      0.10
01:59:46 PM      sdb1      3.03     64.65      0.00     21.33      0.03      9.33      5.33      1.62
01:59:46 PM      sdc1      3.03     64.65      0.00     21.33      0.03      9.33      5.33      1.62
01:59:46 PM      sde1      8.08      0.00    105.05     13.00      0.00      0.38      0.38      0.30
01:59:46 PM      sdf1      8.08      0.00    105.05     13.00      0.00      0.38      0.38      0.30
01:59:46 PM      sda2      1.01      8.08      0.00      8.00      0.01      9.00      9.00      0.91
01:59:46 PM      sdb2      1.01      8.08      0.00      8.00      0.01      9.00      9.00      0.91
```

### Report network statistics (sar -n)
KEYWORD can be one of the following:

* DEV – Displays network devices vital statistics for eth0, eth1, etc.,
* EDEV – Display network device failure statistics
* NFS – Displays NFS client activities
* NFSD – Displays NFS server activities
* SOCK – Displays sockets in use for IPv4
* IP – Displays IPv4 network traffic
* EIP – Displays IPv4 network errors
* ICMP – Displays ICMPv4 network traffic
* EICMP – Displays ICMPv4 network errors
* TCP – Displays TCPv4 network traffic
* ETCP – Displays TCPv4 network errors
* UDP – Displays UDPv4 network traffic
* SOCK6, IP6, EIP6, ICMP6, UDP6 are for IPv6
* ALL – This displays all of the above information. The output will be very long.

```bash
$ sar -n DEV 1 1
Linux 2.6.18-194.el5PAE (dev-db)        03/26/2011      _i686_  (8 CPU)

01:11:13 PM     IFACE   rxpck/s   txpck/s   rxbyt/s   txbyt/s   rxcmp/s   txcmp/s  rxmcst/s
01:11:14 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
01:11:14 PM      eth0    342.57    342.57  93923.76 141773.27      0.00      0.00      0.00
01:11:14 PM      eth1      0.00      0.00      0.00      0.00      0.00      0.00      0.00
```
