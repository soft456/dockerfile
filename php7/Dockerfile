# Version 2.1

FROM 10.5.3.65:5000/nginx:v1.0

MAINTAINER soft456@gmail.com

RUN yum clean all

RUN yum -y install epel-release \
 && yum -y install libxml2 libxml2-devel bzip2 bzip2-devel curl-devel libjpeg libjpeg-devel libpng libpng-devel gmp-devel freetype-devel libmcrypt libmcrypt-devel
 
RUN cd /root
 
RUN echo "==> Configuring ldconfig..." \
 && echo "/usr/local/lib">> /etc/ld.so.conf \
 && ldconfig -v \
 && echo "==> Finishing..."

ENV PHP_VERSION 7.1.1
ENV PHP_PREFIX /usr/local/php

RUN cd /root \
 && echo "==> Downloading PHP..." \
 && wget http://cn2.php.net/distributions/php-${PHP_VERSION}.tar.gz \
 && tar zxvf php-${PHP_VERSION}.tar.gz \
 && echo "==> Configuring PHP..." \
 && cd php-* \
 && ./configure --prefix=/usr/local/php \
    --libdir=/user/local/php/lib/php \
    --with-openssl \
    --with-mcrypt \
    --with-mhash \
    --with-pcre-regex \
    --with-zlib \
    --with-iconv \
    --with-bz2 \
    --with-curl \
    --with-gd \
    --with-jpeg-dir \
    --with-png-dir \
    --with-freetype-dir \
    --with-gettext \
    --with-gmp \
    --with-mhash \
    --with-mysqli \
    --with-pdo-mysql \
    --with-pdo-sqlite \
    --with-libxml-dir \
    --with-libdir=lib64 \
    --enable-fpm \
    --enable-soap \
    --enable-bcmath \
    --enable-exif \
    --enable-filter \
    --enable-ftp \
    --enable-gd-native-ttf \
    --enable-gd-jis-conv \
    --enable-mbstring \
    --enable-pdo \
    --enable-json \
    --enable-session \
    --enable-shmop \
    --enable-simplexml \
    --enable-sockets \
    --enable-sysvsem \
    --enable-sysvshm \
    --enable-wddx \
    --enable-xml \
    --enable-opcache \
    --enable-zip \
    --disable-fileinfo \
    --disable-mbregex \
    --disable-mbregex-backtrack \
    --disable-pcntl \
    --disable-debug \
    --disable-rpath \ 
 && echo "==> Building PHP..." \
 && make \
 && echo "==> Installing PHP..." \
 && make install \
 && echo "==> Finishing..."

RUN ln -sf $PHP_PREFIX/sbin/php /usr/local/bin/php \
 && ln -sf $PHP_PREFIX/sbin/php-fpm /usr/local/bin/php-fpm \
 && cp /root/php*/php.ini-production $PHP_PREFIX/lib/php.ini \
 && rm -rf /root/php*
 
RUN  echo "==> Modify php config..." \
 && cp $PHP_PREFIX/etc/php-fpm.conf.default $PHP_PREFIX/etc/php-fpm.conf \
 && sed -i "s/;pid = run\/php-fpm.pid/pid = run\/php-fpm.pid/g" $PHP_PREFIX/etc/php-fpm.conf \
 && cp $PHP_PREFIX/etc/php-fpm.d/www.conf.default $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/127.0.0.1:9000/\/dev\/shm\/php-fpm-ms.sock/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \ 
 && sed -i "s/;listen.owner = nobody/listen.owner = nobody/g" $PHP_PREFIX/etc/php-fpm.conf \
 && sed -i "s/;listen.group = nobody/listen.group = nobody/g" $PHP_PREFIX/etc/php-fpm.conf \
 && sed -i "s/;listen.mode = 0660/listen.mode = 0660/g" $PHP_PREFIX/etc/php-fpm.conf \  
 && sed -i "s/pm.max_children = 5/pm.max_children = 50/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.start_servers = 2/pm.start_servers = 20/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.min_spare_servers = 1/pm.min_spare_servers = 10/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.max_spare_servers = 3/pm.max_spare_servers = 30/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;pm.max_requests = 500/pm.max_requests = 1000/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;slowlog = log\/$pool.log.slow/slowlog = log\/$pool.log.slow/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;request_slowlog_timeout = 0/request_slowlog_timeout = 2s/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/session.save_handler = files/session.save_handler = redis/g" $PHP_PREFIX/lib/php.ini \
 && sed '/session.save_handler = redis/a\session.save_path = \"tcp://10.5.3.67:6379\"' -i $PHP_PREFIX/lib/php.ini \
 && sed '/; extension_dir = \"ext\"/a\extension_dir = \"\/usr\/local\/php\/lib\/php\/extensions\/no-debug-non-zts-20151012\/\"' -i $PHP_PREFIX/lib/php.ini \
 && sed -i "s/session.cookie_domain =/session.cookie_domain =.bmmyou.com/g" $PHP_PREFIX/lib/php.ini \
 && sed -i "s/;open_basedir =/open_basedir = \/data\/webapp\/www\/:\/tmp\//g" $PHP_PREFIX/lib/php.ini \
 && sed -i "s/expose_php = On/expose_php = Off/g" $PHP_PREFIX/lib/php.ini \
 && echo "yaf.library=\"\/data\/webapp\/www\/yafext\/\"" >> $PHP_PREFIX/lib/php.ini
 
RUN echo "==> Writting rc.local..." \  
 && echo "php-fpm &" >> /etc/rc.local \
 && chmod +x /etc/rc.local

WORKDIR $PHP_PREFIX

CMD /bin/bash /etc/rc.local && /bin/bash

