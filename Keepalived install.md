## 一、简单介绍
1. keepalived提供是生成虚拟公共VIP，这个IP是物理机IP功能相同，提供其安装的机器使用；
2. 可以在安装它的机器上随时自动更换，无需手动切换，达到两台或多台机器使用同一个IP来使用；
3. 安装keepalived的机器，实现的高可用，其中的每个子机器都是拥有相同的功能。

## 二、安装Keepalived

Centos:
```shell
yum install keepalived
```



## 三、Keepalived + Nginx配置

1. MASTER 节点配置文件

```shell
! Configuration File for keepalived

global_defs {
   router_id Centos8-Nginx-MASTER-22
}

vrrp_script chk_nginx {
    script "/etc/keepalived/nginx_check.sh"
    interval 2
    weight -20
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
  track_script {
     chk_nginx
   }
    virtual_ipaddress {
        192.168.1.50
    }
}

```

2. BACKUP 节点配置文件
```shell
! Configuration File for keepalived

global_defs {
   router_id Centos8-Nginx-MASTER-22
}

vrrp_script chk_nginx {
    script "/etc/keepalived/nginx_check.sh"
    interval 2
    weight -20
}

vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 99
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
  track_script {
     chk_nginx
   }
    virtual_ipaddress {
        192.168.1.50
    }
}


```

3. 编写 Nginx 状态检测脚本

```shell

vi /etc/keepalived/nginx_check.sh

# 粘贴以下内容

#!/bin/bash
A=`ps -C nginx --no-header|wc -l`
if [ $A -eq 0 ];then
    /usr/local/nginx/sbin/nginx
sleep 2
if [ `ps -C nginx --no-header|wc -l` -eq 0 ];then
    killall keepalived
fi
fi


```


## 四、启动 Keepalived

```shell

service keepalived stop
service keepalived start
service keepalived status

```

## 五、检测
在Master节点执行以下代码：   
```shell
/usr/local/nginx/sbin/nginx -s stop

```

如果ip到BackUp节点，即代表搭建成功
