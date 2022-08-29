

# docker更换镜像源

注册一个阿里云用户,访问 https://cr.console.aliyun.com/#/accelerator 获取专属Docker加速器地址

https://hgdb8dd9.mirror.aliyuncs.com


```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hgdb8dd9.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
