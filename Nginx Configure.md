# 路径
``` 
编译安装配置文件路径是：/usr/local/nginx/conf/nginx.conf
yum安装配置文件路径是：/etc/nginx/nginx.conf
```


# 常见问题总结

## 1、开启ipv6端口占用  

```shell
# 这一种是端口复用
server {
        listen       80;
        # listen       [::]:80 ipv6only=on;
        listen       [::]:80;
        listen       443;
        # listen       [::]:443 ipv6only=on;
        listen       [::]:443;
        server_name  localhost;
        }


```
