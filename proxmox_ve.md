[在线详细文档](https://pve.proxmox.com/pve-docs/pve-admin-guide.html)

PVE实操过程实录

# 第一节：PVE安装
pve  install  in hardware pc  

1、download image  
* url：<https://www.proxmox.com/en/downloads>   
* version：[proxmox-ve_7.1-2.iso](https://www.proxmox.com/en/downloads?task=callelement&format=raw&item_id=638&element=f85c494b-2b32-4109-b8c1-083cca2b7db6&method=download&args[0]=894a15e72ec01863cc18396f276617b8) 

2、write diver image with ultralISO  
~~~javascript
// step:
-> open ultraliso 
-> start 
-> write diver image
~~~

3、insert usb   &   bios set  
```shell
enable vt or vt-d  
select boot
```

4、start pc  

5、select first option " install proxmox ve "  

6、next-> -> -> ->   

7、target harddisk  

8、country->china  
```shell
time zone -> shanghai
keyboard layout -> u.s.
```

9、set password  and e-mail(can custom)  

10、set hostname  (eg.pve1.xxx.com) and set network and ip -> next  

11、next-> -> -> -> wait....  

12、open web console  
```shell
# 查看web地址
cat /etc/issue

# result
------------------------------------------------------------------------------

Welcome to the Proxmox Virtual Environment. Please use your web browser to 
configure this server - connect to:

  https://192.168.1.8:8006/

------------------------------------------------------------------------------
```

|url段|值|
|----|----|
|schemas|https|
|port|8006|  

# 第二节：PVE时区与时间管理
## 一、时间设置   

### 一）、设置时区  
一般安装时已经选择，无需再设置  

1、 查看当前系统时间  
```shell
date -R
```

2、 如果不是东八区，那么就接着要调整时区  
```shell
tzselect
```

3、 过程略
```shell
# 选择  Asia=>中国（China)=>北京(Beijing)
```

4、 拷贝配置生效  
```shell
sudo cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
```  

5、检查生效情况  
```shell
date -R
```
### 二）、手动校时 
  
```shell
# 修改日期
sudo date -s MM/DD/YY 
# 修改时间   
sudo date -s hh:mm:ss  
# 非常重要，如果没有这一步的话，后面时间还是不准  
sudo hwclock --systohc 
```
### 三）、网络校时 

```shell
apt install ntpdate -y

ntpdate 210.72.145.44    # 中国国家时间服务器: 210.72.145.44
# ntp.ntsc.ac.cn
```

### 四）、Debian自动网络校时  

1、使用Linux crontab 用来定期执行更新时间
|参数|作用|
|----|----|
|-u|用户<font color=gray size=2>（eg. root &nbsp; /&nbsp; user）</font>|
|-r|删除时程表|
|-l|列出时程表|
|-e|执行文本任务<font color=gray size=2>（eg. -e ntpdate ntp.ntsc.ac.cn）</font>|

2、Crontab表达式补充
    2.1 Cron-Expressions的各个子表达式含义
~~~
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
~~~   

2.2 Crontab特殊字符含义
|符号|含义|
|:-:|-|
|/|每又，<font color=gray size=2>(eg. 2/5 * * * *) 表示：每5分钟又2分，等于0+2，5+2...</font>|
|-|在\*与\*之间每，<font color=gray size=2>（eg. 2-5 * * * *）表示：在2与5之间每分钟</font>|
|,|\*和\*的时候<font color=gray size=2>（eg. 2,5 * * * *）表示：在2和5的时候</font>|

2.3 举一些完整的Crontab表达式例子：
<em>注意看大单位定位小单位读法</em>
表达式|含义
|-|-|
|* * * * * |	每天每小时每隔 1 分钟执行一次计划任务|
|0 * * * *	|	每天每小时整点执行一次计划任务|
|15 10 * * *	|	每天 10:15 执行一次计划任务|
|5 12 1 * *	|	每个月 1 号的 12:05 执行一次计划任务|
|10 15 20 3 *	|	每年 3 月 20 日 15:10 执行一次计划任务|
|10 15 * 3 0	|	每年 3 月的每个周日 15:10 执行一次计划任务|
|* 14 * * *	|	每天从 14:00 到 14:59 每隔 1 分钟执行一次触发|
|0/5 14 * * *	|	每天从 14:00 到 14:59 每个 5 分钟执行一次触发|
|0/5 14,18 * * *	|	每天从 14:00 到 14:59 和 18:00 到 18:59 每隔 5 分钟执行一次触发|
|0-5 14 * * *	|	每天从 14:00 到 14:05 每隔 1 分钟执行一次触发|
|10,44 14 * 3 WED	|	3 月的每个星期 3 的 14:10 和 14:44 分别执行一次触发|
|0 0 1 * *	|	每月的第一天执行一次触发|

2.4 添加定时网络校时任务
```shell
# 编辑时程表
crontab -e

# 追加以下内容，每周0点0分0时
0 0 * * 1 ntpdate ntp.ntsc.ac.cn
```

### 五）、Proxmox使用Chrony用作默认的NTP守护程序

1、安装chrony一般已经安装  

```shell
apt install chrony
```

2、配置chrony.conf

```shell
nano /etc/chrony/chrony.conf

#添加
#中国国家授时中心
server ntp.ntsc.ac.cn iburst
server ntp1.aliyun.com
```

3、重启chrony
```shell
systemctl restart chronyd
```

4、chronyd正在访问的当前时间源的信息
```shell
chronyc sources
```

5、查看chrony状态，如果查到无需安装
```shell
systemctl status chronyd
```

6、验证自动同步
通过减少一天时间，等待5分钟查看是否自动同步
```shell
date -s "-1 day"
```


# 第三节：设置PVE的软件源

## 一、查看Debian版本
设置debian软件源时需要，下面以 ***(debian11(bullseye))***  为例设置  
```shell
# 第一种
cat /etc/debian_version

# 第二种
uname -a

# 第三种
hostnamectl

--------------------------------------------------------------------------
#返回
   Static hostname: pve1
         Icon name: computer-desktop
           Chassis: desktop
        Machine ID: ec6d71b0fd6d435aab17f4fce710ed2f
           Boot ID: f6d78431aa27454baaffc981e2c2eac1
   Operating System: Debian GNU/Linux 11 (bullseye)
            Kernel: Linux 5.13.19-2-pve
--------------------------------------------------------------------------

# 第四种
cat /etc/os-release
```

## 二、软件源配置文件
有2种软件仓库构成  
1. Debian的软件源  
2. Proxmox VE专用软件源  
  - 企业源
  - 无订阅源
  - 测试源

PVE软件源的配置文件:  
```shell
# Debian系统源和proxmox源
/etc/apt/sources.list

# Proxmox企业仓库更新源
/etc/apt/sources.list.d/pve-enterprise.list 

# 创建五订阅更新源
touch /etc/apt/sources.list.d/pve-no-subscription.list
```

## 三、常用国内镜像源
```shell
# 清华开源镜像站
https://mirrors.tuna.tsinghua.edu.cn/
# 使用帮助 https://mirrors.tuna.tsinghua.edu.cn/help/proxmox/
# wget https://mirrors.ustc.edu.cn/proxmox/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg

# 中科大学开源软件镜像
https://mirrors.ustc.edu.cn/

## 阿里云（没找到pve的源）
http://mirrors.aliyun.com/debian/
# 使用帮助 https://developer.aliyun.com/mirror/debian
```

## 四、设置PVE更新源    
1、注释掉Proxmox企业版更新源source list  
```shell
# 方法一：注释掉（推荐）
vi /etc/apt/sources.list.d/pve-enterprise.list
# nano /etc/apt/sources.list.d/pve-enterprise.list

# 然后用 '#' 注释掉其中的地址
# deb https://enterprise.proxmox.com/debian/pve buster pve-enterprise

# 方法二：删除
rm -rf /etc/apt/sources.list.d/pve-enterprise.list

# 方法二：更改为备份
mv /etc/apt/sources.list.d/pve-enterprise.list /etc/apt/sources.list.d/pve-enterprise.list.bak

```

2、设置Debian系统源（阿里云源）和proxmox源  
```shell
nano /etc/apt/sources.list
# vi /etc/apt/sources.list
```
修改为：
```shell

# deb http://ftp.debian.org/debian bullseye main contrib
# deb http://ftp.debian.org/debian bullseye-updates main contrib

# security updates
# deb http://security.debian.org bullseye-security main contrib

# debian aliyun source(apt install apt-transport-https -y)
deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb-src http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib

# # debian 清华大学 source，默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
# deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free


# proxmox source
# deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription
# deb https://mirrors.ustc.edu.cn/proxmox/debian/pve bullseye pve-no-subscription
# deb https://mirrors.tuna.tsinghua.edu.cn/proxmox/debian bullseye pve-no-subscription

```
使用nano编辑完之后，使用组合键 <kbd>Ctl</kbd>+<kbd>O</kbd> 保存,使用组合键 <kbd>Ctl</kbd>+<kbd>X</kbd> 退出

3、添加无订阅更新源
```shell
# 可以放到文件中/etc/apt/sources.list
echo 'deb https://mirrors.tuna.tsinghua.edu.cn/proxmox/debian bullseye pve-no-subscription' >> /etc/apt/sources.list.d/pve-no-subscription.list
# echo 'deb https://mirrors.ustc.edu.cn/proxmox/debian/pve buster pve-no-subscription' >> /etc/apt/sources.list.d/
```

4、Ceph源
```shell
vi  /etc/apt/sources.list.d/ceph.list
# nano  /etc/apt/sources.list.d/ceph.list
```
修改为：
```shell
# deb http://download.proxmox.com/debian/ceph-pacific bullseye main
deb https://mirrors.ustc.edu.cn/proxmox/debian/ceph-pacific bullseye main
```
5、执行更新源
```shell
apt update   #更新源
apt upgrade  #更新已安装的包
```
__注__：下面命令请根据自己情况选择执行，可以定期执行升级操作
```shell
apt update && apt dist-upgrade -y #如需升级pve，则执行该命令
```

6、删除不订阅情况下不弹窗
```shell
nano /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
```
<kbd>Ctrl</kbd> + <kbd>W</kbd> &nbsp;搜索 "No valid subscription"

将所在代码块的判断语句从
```javascript
if (res === null || res === undefined || !res || res.data.status.toLowerCase() !== ‘active’)
```
改为

```javascript
if (false)
```
最后注销重新登陆

7、设置CT Templates
需要使用 Proxmox 网页端下载 CT Templates，可以替换 CT Templates 的源

- 方法一：
    将 /usr/share/perl5/PVE/APLInfo.pm 文件中默认的源地址 http://download.proxmox.com 替换为 https://mirrors.tuna.tsinghua.edu.cn/proxmox 即可   
- 方法二：
    将 /usr/share/perl5/PVE/APLInfo.pm 文件中默认的源地址 http://download.proxmox.com 替换为 https://mirrors.ustc.edu.cn/proxmox 即可   

使用命令操作
```shell
cp /usr/share/perl5/PVE/APLInfo.pm /usr/share/perl5/PVE/APLInfo.pm.back

sed -i 's|http://download.proxmox.com|https://mirrors.tuna.tsinghua.edu.cn/proxmox|g' /usr/share/perl5/PVE/APLInfo.pm
```

## 五、KDE Plasma桌面环境
```shell
apt-get install task-kde-desktop
```

# 第四节：网络设置

## 一、PVE开启IPV6

查看内核开启ipv6自动配置：
```shell
cat /proc/sys/net/ipv6/conf/vmbr0/accept_ra

# 返回
1
cat /proc/sys/net/ipv6/conf/vmbr0/autoconf

# 返回
1
```

查看已开启ipv6转发：
```shell
cat /proc/sys/net/ipv6/conf/vmbr0/forwarding

# 返回
1
```

需要将accept_ra值改成2才能自动配置SLAAC ipv6地址：

```shell
nano /etc/sysctl.conf

# 在文件中追加
net.ipv6.conf.all.accept_ra=2
net.ipv6.conf.default.accept_ra=2
net.ipv6.conf.vmbr0.accept_ra=2
net.ipv6.conf.all.autoconf=1
net.ipv6.conf.default.autoconf=1
net.ipv6.conf.vmbr0.autoconf=1
```

重启网络服务
```shell
service networking restart
```


# 第四节：存储  

挂载磁盘方式：  
> 本地磁盘：目录（常用）、LVM、ZFS  
> 网络存储：ceph、iscsi、nfs、cifs  

## 一）默认存储  
1、查看磁盘
```shell
fdisk -l

# pve-root  作为根目录   
# pve-swap  作为虚拟内存  
# pve-data  作为磁盘镜像储存
```

LVM还建了一个thinpool，名为data  
从运维的角度，建议直接不使用lvm-thin（pve节点-磁盘-lvm-thin），并且将所有空间给到pve-root  

## 二）合并存储lvm存储   
*PVE删除Local-lvm（实际是lvm-thin）存储空间并合并到local中  

1、删除local-lvm存储空间   
```shell
lvremove pve/data
# or lvremove /dev/pve/data
```  

2、再将lvm-thin的空间转移到pve-root  
```shell
lvextend -l +100%FREE -r pve/root
# or lvextend -L +100%FREE -r /dev/pve/root
```  

3、使用resize2fs调整文件系统大小  
```shell
resize2fs /dev/mapper/pve-root
```

4、查看大小/dev/mapper/pve-root  
```shell
df -Th
```  

5、修改一下储存配置：删除web界面local-lvm(即lvm-thin)  
~~~javascript
// 路径 
数据中心 -> 存储-local -> lvm -> 删除

//编辑local
目录 -> 内容 -> 添加 -> 磁盘映像和容器
~~~



# 第五节：集群  

## 一）创建集群  
```shell
# 数据中心 -> 集群 -> 创建集群
```

## 二）加入集群  
```shell
# 数据中心 -> 集群 -> 创建集群
```

## 三）退出集群  
1、在待隔离节点上停止 pve-cluster 服务  
```shell
systemctl stop pve-cluster.service
systemctl stop corosync.service
```  

2、 将待隔离节点的集群文件系统设置为本地模式  
```shell
pmxcfs -l
```  

3、 删除corosync 配置文件  
```shell
rm /etc/pve/corosync.conf
rm -rf /etc/corosync/*
```  

4、 重启cluster集群文件系统服务  
```shell
killall pmxcfs
systemctl start pve-cluster.service
```

5、 离线节点删除 WEB-UI 上的除当前 node 外的 node  
```shell
cd /etc/pve/nodes
rm -rf ***
# or
# rm -rf node_name(eg.pve2)
```

6、清理集群残留信息（正常的节点）  
```shell
cd /etc/pve/nodes
rm -rf ***
pvecm delnode NodeName(eg.pve2)
```   


## ）故障  
1、Proxmox服务器SSH可以登录但WebUI无法访问  
```shell
# 重启网页服务
systemctl restart pveproxy pvedaemon
```




