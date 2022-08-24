# centos 8 adjust source

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
#  清除缓存
yum clean all && yum makecache

# 可以直接升级
yum update
```
