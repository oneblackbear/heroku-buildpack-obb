Silex build pack
========================

This is a build pack for Heroku for OBB PHP-based apps. This is based on the [heroku-buildpack-php](https://github.com/heroku/heroku-buildpack-php).

Using
-----

To use this buildpack, just create a new app:

    heroku create --stack cedar --buildpack https://github.com/oneblackbear/heroku-buildpack-obb.git

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
    wget http://us.php.net/get/php-5.4.4.tar.gz/from/us.php.net/mirror
    mv mirror php-5.4.4.tar.gz
    tar xzvf php-5.4.4.tar.gz
    cd php-5.4.4/
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql --with-iconv --with-gd --with-config-file-path=/app/php --enable-soap=shared --with-openssl --enable-mbstring --with-iconv
    make
    make install
    cd ..

    # PHP Extensions
    cp php-5.4.4/libs/libphp5.so /app/apache/modules/
    cp /usr/lib64/libmysqlclient.so /app/php/ext/
    cp /usr/lib64/libmysqlclient.so.18 /app/php/ext/
    cp /usr/lib/libmcrypt.so /app/php/ext/
    cp /usr/lib/libmcrypt.so.4 /app/php/ext/
    wget http://pecl.php.net/get/APC
    tar -zxvf APC
    cd APC-3.1.10
    /app/php/bin/phpize
    ./configure --enable-apc --enable-apc-mmap --with-php-config=/app/php/bin/php-config
    make
    make install

    # Create packages
    cd /app
    echo '2.2.22' > apache/VERSION
    tar -zcvf apache.tar.gz apache
    echo '5.4.4' > php/VERSION
    tar -zcvf php.tar.gz php


Hacking
-------

To change this buildpack, fork it on Github. Push up changes to your fork, then create a test app with --buildpack <your-github-url> and push to it.


Meta
----

Original build pack created by Pedro Belo. Composer support by Henri Bergius. Silex build pack by Klaus Silveira.
