# Version 1.0

FROM 10.5.3.65:5000/nginx:v1.12

MAINTAINER soft456@gmail.com

RUN yum clean all

RUN yum -y install epel-release \
 && yum -y install libxml2 libxml2-devel bzip2 bzip2-devel curl-devel libjpeg libjpeg-devel libpng libpng-devel gmp-devel \
 && yum -y install freetype-devel libmcrypt libmcrypt-devel neon-devel git m4 autoconf

RUN cd /root \
RUN echo "==> Configuring ldconfig..." \
 && echo "/usr/local/lib">> /etc/ld.so.conf \
 && ldconfig -v \
 && echo "==> Finishing..."

ENV PHP_VERSION 7.1.6 
ENV PHP_PREFIX /usr/local/php
 
RUN cd /root \
 && echo "==> Downloading PHP..." \
 && wget http://cn2.php.net/distributions/php-${PHP_VERSION}.tar.gz \
 && tar zxvf php-${PHP_VERSION}.tar.gz \
 && echo "==> install PHP..." \
 && cd php-* \
 && ./configure \
    --prefix=/usr/local/php \
    --libdir=/usr/local/php/lib \
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
 && make \
 && make install \
 && echo "==> Finishing..."
 
RUN mkdir -p $PHP_PREFIX/{lib,log}
RUN ln -sf $PHP_PREFIX/sbin/php /usr/local/bin/php \
 && ln -sf $PHP_PREFIX/sbin/php-fpm /usr/local/bin/php-fpm \
 && cp /root/php*/php.ini-production $PHP_PREFIX/lib/php.ini \
 && rm -rf /root/php*
 
RUN echo "==> Modify php config..." \
 && cp $PHP_PREFIX/etc/php-fpm.conf.default $PHP_PREFIX/etc/php-fpm.conf \
 && sed -i "s/;pid = run\/php-fpm.pid/pid = run\/php-fpm.pid/g" $PHP_PREFIX/etc/php-fpm.conf \
 && cp $PHP_PREFIX/etc/php-fpm.d/www.conf.default $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/127.0.0.1:9000/\/dev\/shm\/php-fpm.sock/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;listen.owner = nobody/listen.owner = nobody/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;listen.group = nobody/listen.group = nobody/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;listen.mode = 0660/listen.mode = 0660/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.max_children = 5/pm.max_children = 50/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.start_servers = 2/pm.start_servers = 20/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.min_spare_servers = 1/pm.min_spare_servers = 10/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/pm.max_spare_servers = 3/pm.max_spare_servers = 30/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;pm.max_requests = 500/pm.max_requests = 1000/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;slowlog = log\/\$pool.log.slow/slowlog = log\/\$pool.log.slow/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/;request_slowlog_timeout = 0/request_slowlog_timeout = 2s/g" $PHP_PREFIX/etc/php-fpm.d/www.conf \
 && sed -i "s/session.save_handler = files/session.save_handler = redis/g" $PHP_PREFIX/lib/php.ini \
 && sed '/session.save_handler = redis/a\session.save_path = \"tcp://10.5.3.67:6379\"' -i $PHP_PREFIX/lib/php.ini \
 && sed '/; extension_dir = \"ext\"/a\extension_dir = \"\/usr\/local\/php\/lib\/extensions\/no-debug-non-zts-20160303\/\"' -i $PHP_PREFIX/lib/php.ini \
 && sed -i "s/session.cookie_domain =/session.cookie_domain =.bmmyou.com/g" $PHP_PREFIX/lib/php.ini \
 && sed -i "s/;open_basedir =/open_basedir = \/data\/webapp\/www\/:\/tmp\//g" $PHP_PREFIX/lib/php.ini \
 && sed -i "s/expose_php = On/expose_php = Off/g" $PHP_PREFIX/lib/php.ini \
 && echo "yaf.library=\"/data/webapp/www/yafext/\"" >> $PHP_PREFIX/lib/php.ini

ENV APCU_VERSION 5.1.8
ENV YAF_VERSION 3.0.5
ENV MEMCACHED_VERSION 3.0.3
ENV REDIS_VERSION 3.1.2
ENV SWOOLE_VERSION 1.9.15
ENV MONGODB_VERSION 1.2.9
ENV XDEBUG_VERSION 2.5.5
 
RUN cd /root \
 && echo "==> Install apcu..." \
 && wget https://pecl.php.net/get/apcu-${APCU_VERSION}.tgz \
 && tar zxvf apcu-${APCU_VERSION}.tgz \
 && cd apcu* \
 && $PHP_PREFIX/bin/phpize \
 && ./configure --enable-apcu --with-php-config=$PHP_PREFIX/bin/php-config \
 && make \
 && make install \
 && cd /root \
 && rm -rf /root/apcu* \
 && echo "extension=apcu.so" >> $PHP_PREFIX/lib/php.ini \
 && echo "==> Install yaf..." \
 && wget https://pecl.php.net/get/yaf-${YAF_VERSION}.tgz \
 && tar zxvf yaf-${YAF_VERSION}.tgz \
 && cd yaf* \
 && $PHP_PREFIX/bin/phpize \
 && ./configure --enable-yaf --with-php-config=$PHP_PREFIX/bin/php-config \
 && make \
 && make install \
 && cd /root \
 && rm -rf /root/yaf* \
 && echo "extension=yaf.so" >> $PHP_PREFIX/lib/php.ini \
 && echo "==> Install libmemcached..." \
 && wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz \
 && tar zxvf libmemcached-1.0.18.tar.gz \
 && cd libmemcached-1.0.18 \
 && ./configure \
 && make \
 && make install \
 && cd /root \
 && rm -rf /root/libmemcached* \
 && echo "==> Install memcached..." \
 && wget https://pecl.php.net/get/memcached-${MEMCACHED_VERSION}.tgz \
 && tar zxvf memcached-${MEMCACHED_VERSION}.tgz \
 && cd memcached* \
 && $PHP_PREFIX/bin/phpize \
 && ./configure --disable-memcached-sasl --with-php-config=$PHP_PREFIX/bin/php-config \
 && make \
 && make install \
 && cd /root \
 && rm -rf /root/memcached* \
 && echo "extension=memcached.so" >> $PHP_PREFIX/lib/php.ini \
 && echo "==> Install redis..." \
 && wget https://pecl.php.net/get/redis-${REDIS_VERSION}.tgz \
 && tar zxvf redis-${REDIS_VERSION}.tgz \
 && cd redis* \
 && $PHP_PREFIX/bin/phpize \
 && ./configure --enable-redis --with-php-config=$PHP_PREFIX/bin/php-config \
 && make \
 && make install \
 && cd /root \
 && rm -rf /root/redis* \
 && echo "extension=redis.so" >> $PHP_PREFIX/lib/php.ini \
 && echo "==> Install swoole..." \
 && wget https://pecl.php.net/get/swoole-${SWOOLE_VERSION}.tgz \
 && tar zxvf swoole-${SWOOLE_VERSION}.tgz \
 && cd swoole* \
 && $PHP_PREFIX/bin/phpize \
 && ./configure --with-php-config=$PHP_PREFIX/bin/php-config \
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/swoole* \ 
 && echo "extension=swoole.so" >> $PHP_PREFIX/lib/php.ini \ 
 && echo "==> Install mongodb..." \ 
 && wget https://pecl.php.net/get/mongodb-${MONGODB_VERSION}.tgz \ 
 && tar zxvf mongodb-${MONGODB_VERSION}.tgz \ 
 && cd mongodb* \ 
 && $PHP_PREFIX/bin/phpize \ 
 && ./configure --enable-mongodb --with-php-config=$PHP_PREFIX/bin/php-config \ 
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/mongodb* \ 
 && echo "extension=mongodb.so" >> $PHP_PREFIX/lib/php.ini \ 
 && echo "==> Install xdebug..." \ 
 && wget https://pecl.php.net/get/xdebug-${XDEBUG_VERSION}.tgz \ 
 && tar zxvf xdebug-${XDEBUG_VERSION}.tgz \ 
 && cd xdebug* \ 
 && $PHP_PREFIX/bin/phpize \ 
 && ./configure --enable-xdebug --with-php-config=$PHP_PREFIX/bin/php-config \ 
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/xdebug* \ 
 && echo "zend_extension=xdebug.so" >> $PHP_PREFIX/lib/php.ini \ 
 && echo "==> Install xhprof..." \ 
 && git clone https://github.com/yaoguais/phpng-xhprof.git ./xhprof \ 
 && cd xhprof \ 
 && $PHP_PREFIX/bin/phpize \ 
 && ./configure --with-php-config=$PHP_PREFIX/bin/php-config \ 
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/xhprof* \ 
 && echo "extension=phpng_xhprof.so" >> $PHP_PREFIX/lib/php.ini \ 
 && echo "==> Install re2c..." \ 
 && wget https://sourceforge.net/projects/re2c/files/0.16/re2c-0.16.tar.gz \ 
 && tar zxf re2c-0.16.tar.gz \ 
 && cd re2c-0.16 \ 
 && ./configure \ 
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/re2c* \ 
 && echo "==> Install mogilefs..." \ 
 && git clone https://github.com/lstrojny/pecl-mogilefs.git ./mogilefs \ 
 && cd mogilefs \ 
 && $PHP_PREFIX/bin/phpize \ 
 && ./configure --with-php-config=$PHP_PREFIX/bin/php-config \ 
 && make \ 
 && make install \ 
 && cd /root \ 
 && rm -rf /root/mogilefs* \ 
 && echo "extension=mogilefs.so" >> $PHP_PREFIX/lib/php.ini
 
 RUN echo "==> Writting rc.local..." \  
 && echo "php-fpm &" >> /etc/rc.local \
 && chmod +x /etc/rc.local

WORKDIR $PHP_PREFIX

CMD /bin/bash /etc/rc.local && /bin/bash
