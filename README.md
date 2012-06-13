Silex build pack
========================

This is a build pack for Heroku for Silex-based apps. This is based on the [heroku-buildpack-php](https://github.com/heroku/heroku-buildpack-php).

Compiling binaries
------------------

In order to create the packages used by the build pack, you must compile Apache, PHP and their extensions with the following steps. The default packages were built on Ubuntu 10.04 64 bits in order to be compatible with Heroku dynos.

    # Compiling Apache
    mkdir /app
    wget http://www.us.apache.org/dist/httpd/httpd-2.2.22.tar.gz
    tar xvzf httpd-2.2.22.tar.gz
    cd httpd-2.2.22
    ./configure --prefix=/app/apache --enable-rewrite --enable-so --enable-deflate --enable-expires --enable-headers
    make
    make install
    cd ..

    # Apache libraries
    mkdir /app/php/ext
    cp /usr/lib/libapr-1.so /app/php/ext
    cp /usr/lib/libaprutil-1.so /app/php/ext
    cp /usr/lib/libapr-1.so.0 /app/php/ext
    cp /usr/lib/libaprutil-1.so.0 /app/php/ext

    # Compiling PHP
    wget http://us.php.net/get/php-5.4.3.tar.gz/from/us.php.net/mirror
    mv mirror php-5.4.3.tar.gz
    tar xzvf php-5.4.3.tar.gz
    cd php-5.4.3/
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql --with-iconv --with-gd --with-config-file-path=/app/php --enable-soap=shared --with-openssl --enable-mbstring --with-iconv
    make
    make install
    cd ..

    # PHP Extensions
    cp php-5.4.3/libs/libphp5.so /app/apache/modules/
    cp /usr/lib64/libmysqlclient.so /app/php/ext/
    cp /usr/lib64/libmysqlclient.so.18 /app/php/ext/
    cp /usr/lib/libmcrypt.so /app/php/ext/
    cp /usr/lib/libmcrypt.so.4 /app/php/ext/
    apt-get install php5-dev php-pear
    pear config-set php_dir /app/php
    pecl install apc
    mkdir /app/php/include/php/ext/apc
    cp /usr/lib/php5/20090626/apc.so /app/php/ext/
    cp /usr/include/php5/ext/apc/apc_serializer.h /app/php/include/php/ext/apc/
    pecl install memcache
    cp /usr/lib/php5/20090626/memcache.so /app/php/ext/

    # Create packages
    cd /app
    echo '2.2.22' > apache/VERSION
    tar -zcvf apache.tar.gz apache
    echo '5.4.3' > php/VERSION
    tar -zcvf php.tar.gz php


Hacking
-------

To change this buildpack, fork it on Github. Push up changes to your fork, then create a test app with --buildpack <your-github-url> and push to it.


Meta
----

Original build pack created by Pedro Belo. Composer support by Henri Bergius. Silex build pack by Klaus Silveira.
