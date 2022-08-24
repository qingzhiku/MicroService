# centos 8 adjust source更换源

## 第一步:
```shell
# 备份现有的repo配置文件
rename '.repo' '.repo.bak' /etc/yum.repos.d/*.repo
```

## 第二步:
```shell
cd /etc/yum.repos.d/
```

## 第三步:

```shell
# 推荐curl，因为有时wget可能没安装

#  下载阿里云源的文件
curl -O https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
curl -O https://mirrors.aliyun.com/repo/epel-archive-8.repo

# wget方式
# wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
# wget -O /etc/yum.repos.d/CentOS-Base.repo  http://mirrors.aliyun.com/epel/epel-release-latest-8.noarch.rpm

```

## 第四步:
```shell
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*

sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
```

## 第五步:
```shell
#  清除缓存
yum clean all && yum makecache

# 可以直接升级
yum update
```
## 第五步:
```shell
# 测试

yum install wget –y
```

# 安装SSH服务

```shell

yum install openssh-server

yum install net-tools

yum install vim

vim /etc/ssh/sshd_config

# 把Port前的注释（即#）去掉

# PermitRootLogin yes #设置是否允许root登录，默认允许 

# 启用
systemctl start sshd.service


# 查看状态
systemctl status sshd.service


# 开机启动
systemctl enable sshd.service

# 查看网口是否开放
netstat -an | grep 22

```

