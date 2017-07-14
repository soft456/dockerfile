# dockerfile
常用dockerfile

## nginx 镜像
包含centos 7.3.1611 和 nginx 1.12.1

#### 下载及镜像编译：
git clone https://github.com/soft456/dockerfile.git
cd nginx
docker build -t nginx:v1.12 .

#### 创建容器：
docker run -it -d --name=nginx -v /etc/localtime:/etc/localtime:ro -v /webapp/www:/webapp/www:Z -p 81:80 nginx:v1.12 /bin/bash

## php7 镜像  
包含：
centos 7.3.1611  
nginx 1.12.1  
php 7.1.6  
apcu 5.1.8  
yaf 3.0.5  
memcached 3.0.3  
redis 3.1.2  
swoole 1.9.15  
mongodb 1.2.9  
xdebug 2.5.5  
xhrpof 0.9.5  
mogilefs 0.9.49  

#### 创建php7容器：
docker run -it -d --name=php7 -v /etc/localtime:/etc/localtime:ro -v /webapp/www:/webapp/www:Z -p 82:80 php7:v2.2 /bin/bash