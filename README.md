Silex build pack
========================

This is a build pack for Heroku for OBB PHP-based apps. This is based on the [heroku-buildpack-php](https://github.com/heroku/heroku-buildpack-php).


Compiling binaries
------------------

In order to create the packages used by the build pack, you must compile Apache, PHP and their extensions with the following steps. The default packages were built on Ubuntu 10.04 64 bits in order to be compatible with Heroku dynos.

    # Compiling Apache
    mkdir /app
    wget http://www.us.apache.org/dist/httpd/httpd-2.2.25.tar.gz
    tar xvzf httpd-2.2.25.tar.gz
    cd httpd-2.2.25
    ./configure --prefix=/app/apache --enable-rewrite --enable-so --enable-deflate --enable-expires --enable-headers
    make
    make install
    cd ..

    # Apache libraries
    mkdir /app/php/ext
    cp /app/apache/lib/libapr-1.so /app/php/ext
    cp /app/apache/libaprutil-1.so /app/php/ext
    cp /app/apache/libapr-1.so.0 /app/php/ext
    cp /app/apache/libaprutil-1.so.0 /app/php/ext

    # Compiling PHP
    wget http://us.php.net/get/php-5.5.4.tar.gz/from/us.php.net/mirror
    mv mirror php-5.5.4.tar.gz
    tar xzvf php-5.5.4.tar.gz
    cd php-5.5.4/
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-iconv --with-gd --with-config-file-path=/app/php --enable-soap=shared --with-openssl --enable-mbstring --with-iconv --with-curl=/usr/lib --with-openssl --with-jpeg-dir --with-png-dir
    make
    make install
    cd ..

    # Create packages
    cd /app
    echo '2.2.25' > apache/VERSION
    tar -zcvf apache.tar.gz apache
    echo '5.5.4' > php/VERSION
    tar -zcvf php.tar.gz php


