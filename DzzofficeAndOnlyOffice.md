采用Docker方式：

前提，安装mysql
```shell
docker run -d --name mysql_dzz -p 33061:3306 -e MYSQL_ROOT_PASSWORD=root -v /mnt/dzzoffice/mysql/data:/var/lib/mysql mysql:5.7.27
```

安装Dzzoffice
```shell
docker run -d --name dzzoffice -p 8001:80 -v /mnt/dzzoffice/dzzofice_data:/var/www/html/data imdevops/dzzoffice:latest

docker exec -it dzzoffice bash

chown -R www-data:www-data /var/www/html/data
```

安装onlyoffice
```shell
docker run -itd --name onlyoffice_documentserver -p 8002:80 onlyoffice/documentserver

```
