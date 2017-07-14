# Version 1.0

FROM  docker.io/centos:7.3.1611

MAINTAINER soft456@gmail.com

RUN yum -y clean all && yum -y update
RUN yum -y install wget gcc gcc-c++ perl make pcre-devel openssl-devel

RUN groupadd -f www \
 && useradd -d /var/cache/nginx -s /sbin/nologin -g www nginx \
 && mkdir /var/log/nginx \
 && mkdir /usr/local/nginx \
 && chown -R nginx.www /var/log/nginx \
 && chown -R nginx.www /usr/local/nginx


ENV NGINX_VERSION 1.12.1
ENV NGINX_PREFIX /usr/local/nginx
ENV NGINX_LOG /var/log/nginx/

RUN cd /root \
 && echo "==> Downloading Nginx..." \
 && wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
 && tar zxvf nginx-${NGINX_VERSION}.tar.gz \
 && echo "==> Configuring Nginx..." \
 && cd nginx-* \
 && ./configure \
    --prefix=$NGINX_PREFIX \
	--user=nginx \
	--group=www \
	--pid-path=/run/nginx.pid \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \    
 && echo "==> Building Nginx..." \
 && make \
 && echo "==> Installing Nginx..." \
 && make install \
 && echo "==> Finishing..."

RUN ln -sf $NGINX_PREFIX/sbin/nginx /usr/local/bin/nginx \
 && rm -rf /root/nginx* \
 && mkdir $NGINX_PREFIX/conf/sites
 
ADD ./files/nginx.conf $NGINX_PREFIX/conf/nginx.conf
ADD ./files/ip.conf $NGINX_PREFIX/conf/sites/ip.conf 
ADD ./files/safe.conf $NGINX_PREFIX/conf/safe.conf

RUN echo "==> Writting rc.local..." \  
 && sed -i '/^touch/d' /etc/rc.local \
 && echo "nginx &" >> /etc/rc.local \
 && chmod +x /etc/rc.local

EXPOSE 80 443
 
WORKDIR $NGINX_PREFIX

CMD /bin/bash /etc/rc.local && /bin/bash
