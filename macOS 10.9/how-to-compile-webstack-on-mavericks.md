# How to compile the a PHP-Nginx WebStack on MacOS X 10.9.x (Mavericks)

##  Before you start

### mySql 5.6.29

Install **mySql** downloading the `.dmg` from Oracle's website.

Add `/usr/local/mysql/bin:` to the bash profile

Later (when installed PHP) check mySql socket path (usually in `/tmp/mysql.sock`) matches with the one specified in `php.ini` usually `/var/mysql/mysql.sock`. Either change `php.ini` or make a symlink.

### mySql reset root password

Run MySQL service from the Preference panel then

	mysqladmin -u root password cocacola
	mysqladmin -u root -pcocacola -h localhost password cocacola
	mysqladmin -u root -pcocacola reload    
    
If in trouble below is a good reference:

**https://akrabat.com/setting-up-php-mysql-on-os-x-mavericks/**

### xCode command line tools

Enable them with

    xcode-select --install
    
### xQuartz

Install it from **http://www.xquartz.org**

##	Sources needed

- autoconf-2.69.tar.gz
- icu4c-4_8_1_1-src.tgz
- *imap-2007f.tar.gz* (optional)
- jpegsrc.v9a.tar.gz
- *libiodbc-3.52.10.tar.gz* (optional)
- libmcrypt-2.5.8.tar.gz
- pcre-8.37.zip
- openssl-1.0.2k.tar.gz
- php-5.6.30.tar.gz
- redis-3.0.7.tar.gz (optional)
- nginx-1.8.1.tar.gz

## How to compile it...

untar or unzip everything

    $ tar -xvf filename.tar.gz

compile and install everything in `/usr/local`

how can you do that? let's have a closer step by step look...

###	Bash Profile

Check bash profile files

	.bash_profile
	.bashrc
	
For exotic/specific configuration or patches. They may screw up the build process.
In case you think you need them then comment `#` everything is not a "default"

###	System Variables

set the following variables in your environment or just type them in the terminal beforehand to optimize the compile process:

    MACOSX_DEPLOYMENT_TARGET=10.9
    CFLAGS="-O3 -fno-common -arch x86_64"
    CCFLAGS="-O3 -arch x86_64 -no-cpp-precomp"
    CXXFLAGS="-O3 -fno-common -arch x86_64"
    LDFLAGS="-O3 -arch x86_64"
    EXTRA_LIBS="-lresolv"
    export CFLAGS CXXFLAGS LDFLAGS CCFLAGS EXTRA_LIBS MACOSX_DEPLOYMENT_TARGET

### Compile sources

compile the following sources and install them...

*the order is not technically important, but this order works!*

#### - autoconf

    $ ./configure --prefix=/usr/local
    
    $ make
    
    $ sudo make install
    
#### - icu4c

    $ cd source

    $ ./configure --prefix=/usr/local
    
    $ make
    
    $ sudo make install
    
#### - imap-2007f

	$ make osx
	
	$ sudo cp c-client/*.h /usr/local/include/
	
	$ sudo cp c-client/*.c /usr/local/lib/
	
	$ sudo cp c-client/c-client.a /usr/local/lib/libc-client.a

#### - jpeg9a

	$ ./configure --prefix=/usr/local
	
	$ make
	
	$ sudo make install
	
#### - libiodbc

	$ ./configure --prefix=/usr/local
	
	$ make
	
	$ sudo make install
	
#### - libmcrypt

	$ ./configure --prefix=/usr/local
	
	$ make
	
	$ sudo make install
	
#### - pcre

	$ ./configure --prefix=/usr/local --enable-utf8 --enable-unicode-properties
	
	$ make
	
	$ sudo make install
	
#### - openssl

    $ ./Configure darwin64-x86_64-cc
    $ make -j 8
    $ sudo make install
    
l'installazione avviende di default su `/usr/local`
la versione `1.0.2k` è l'ultima compatibile con PHP `5.6.x`
	
#### - php

	$ ./configure \
		--enable-fpm \
		--prefix=/usr/local \
		--mandir=/usr/local/share/man \
		--infodir=/usr/local/share/info \
		--enable-cli \
		--with-config-file-path=/usr/local/php/etc \
		--sysconfdir=/usr/local/php/etc \
		--enable-exif \
		--enable-ftp \
		--with-gd \
		--with-jpeg-dir=/usr/local/lib \
		--with-png-dir=/usr/X11 \
		--enable-gd-native-ttf \
		--with-freetype-dir=/usr/X11 \
		--with-kerberos \
		--enable-mbstring \
		--enable-mbregex \
		--enable-json \
		--with-mysql=mysqlnd \
		--with-mysqli=mysqlnd \
		--enable-pdo \
		--with-pdo-mysql=mysqlnd \
		--with-mysql-sock=/tmp/mysql.sock \
		--enable-shmop \
		--with-snmp=/usr \
		--enable-sockets \
		--with-mhash \
		--with-mcrypt \
		--enable-xml \
		--with-libxml-dir=/usr \
		--with-xmlrpc \
		--enable-xmlwriter \
		--enable-xmlreader \
		--enable-dom \
		--with-xsl=/usr \
		--with-pcre-regex=/usr/local \
		--enable-fileinfo \
		--enable-zip \
		--with-curl \
		--with-openssl \
		--with-zlib \
		--enable-intl \
		--with-icu-dir=/usr/local \
		--enable-opcache \
		\
		--enable-maintainer-zts \
		--with-imap-ssl \
		--with-iodbc=/usr/local \
		


	$ make
	
	$ sudo make install

`--enable-maintainer-zts` allows PHP multithreading. If not needed **should** be omitted.

`--with-imap-ssl` set it if you have installed optional **imap**

`--with-iodbc=/usr/local` set it if you have installed optional **odbc**

`--sysconfdir=/usr/local/php/etc` have been changed from `/private/etc` (previous config.) to the actual value in order to have `php-fpm.conf` and other php related stuff into the new specified location.


#### - redis

	$ make

    $ sudo mkdir /usr/local/redis
    $ sudo mkdir /usr/local/redis/bin
    $ sudo mkdir /usr/local/redis/etc
    
    $ sudo cp redis-server /usr/local/redis/bin
    $ sudo cp redis-cli /usr/local/redis/bin
    
    $ sudo ln -s /usr/local/bin/redis-server /usr/local/redis/bin/redis-server
    $ sudo ln -s /usr/local/bin/redis-cli /usr/local/redis/bin/redis-cli
	
	$ sudo cp redis.conf /usr/local/redis/etc
	

	
#### - nginx

	$ ./configure \
	    --prefix=/usr/local \
	    --sbin-path=/usr/local/sbin/nginx \
	    --conf-path=/usr/local/nginx/nginx.conf \
	    --with-http_ssl_module \
	    --with-http_stub_status_module \
	    --with-http_spdy_module \
	    --with-pcre=../pcre-8.37 \
	    --add-module=../headers-more-nginx-module-0.28

	$ make
	
	$ sudo make install
	
The modue `headers-more` in the **specified version**

## can be downloaded here 

**https://github.com/openresty/headers-more-nginx-module**

and must be present on the parent directory when running configure.

#### UPDATE: - nginx 1.12.2

configure and make **PCRE**

check **OPENSSL** is not the system default but version `1.1.0d` with `$ openssl version`

located in `/usr/local/bin/`

If it's not compile it with the instructions above.

Uncompact **headers more** v.0.33

Finally

```
$ ./configure \
    --prefix=/usr/local \
    --sbin-path=/usr/local/sbin/nginx \
    --conf-path=/usr/local/nginx/nginx.conf \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-pcre=../pcre-8.37 \
    --with-http_v2_module \
    --add-module=../headers-more-nginx-module-0.33

$ make

$ sudo make install
```

Note that **spdy** is deprecated and substituted by **HTTP v2**

Check which OpenSSL version nginx is using with `nginx -V`

	
## Notes

this document assumes you want to install **php** with **php-fpm** support (it's the best choice to use it with **NginX**)

to make it happen correctly you have to install **Xcode Command Line Tools**:

	$ xcode-select --install

this will install command line tools needed and provide headers needed to compile all the sources in `/usr/include`

the version of the sources in this document are the current as of 2015-11-13 (it's friday the 13th...)

to follow this guide you are supposed to have previously installed **mySQL** and **XQuartz** (they will provide headers for mysql and freetype respectively)

to avoid any issue with MacOS X native php version the **php.ini** file will be placed in `/usr/local/php/etc`

if you followed this guide you should now be safe and sound... using php happily on your Mac! (:

*for newer or older MacOS X versions this guide might need some adjustments...*

## Packages

To easily install packages (redis, pthreads..) **PECL** is needed.

**PEAR** + **PECL** should have been built and installed with **PHP**. **PECL** needs `autoconf` (that's why it's in the build recipe).

If **PEAR** / **PECL** are not installed follow the instructions below

Download PEAR (includes PECL) in the current sources directory

    curl -O http://pear.php.net/go-pear.phar
    
PEAR is installed with PHP with root privileges

    sudo /usr/local/bin/php -d detect_unicode=0 go-pear.phar
    
Type `1` and press `return`.

Enter:

`/usr/local/pear`
Type `4` and press `return`.
Enter:

`/usr/local/bin`
Press `return`

Press `return` again


Verify PEAR
You should be able to type:

`/usr/local/bin/pear version`   

Finally check include paths

**http://pear.php.net/manual/en/installation.checking.php**

Every time an extension is added the **php.ini** file must be updated, example:

	extension=pthreads.so
	extension=redis.so

    
## pthreads module

`sudo /usr/local/bin/pecl install pthreads-2.0.10`

## redis module

`sudo /usr/local/bin/pecl install redis`

# php locale configure

To install an additional PHP build in a different directory use a configue like this:

    ./configure \
        --prefix=/Users/administrator/phpzts \
        --mandir=/Users/administrator/phpzts/share/man \
        --infodir=/Users/administrator/phpzts/share/info \
        --enable-cli \
        --with-config-file-path=/Users/administrator/phpzts/php/etc \
        --sysconfdir=/Users/administrator/phpzts/php/etc \
        --enable-exif \
        --enable-ftp \
        --with-gd \
        --with-jpeg-dir=/usr/local/lib \
        --with-png-dir=/usr/X11 \
        --enable-gd-native-ttf \
        --with-freetype-dir=/usr/X11 \
        --with-kerberos \
        --enable-mbstring \
        --enable-mbregex \
        --enable-json \
        --with-mysql=mysqlnd \
        --with-mysqli=mysqlnd \
        --enable-pdo \
        --with-pdo-mysql=mysqlnd \
        --with-mysql-sock=/tmp/mysql.sock \
        --enable-shmop \
        --with-snmp=/usr \
        --enable-sockets \
        --with-mhash \
        --with-mcrypt \
        --enable-xml \
        --with-libxml-dir=/usr \
        --with-xmlrpc \
        --enable-xmlwriter \
        --enable-xmlreader \
        --enable-dom \
        --with-xsl=/usr \
        --with-pcre-regex=/usr/local \
        --enable-fileinfo \
        --enable-zip \
        --with-curl \
        --with-openssl=shared \
        --with-zlib \
        --enable-intl \
        --with-icu-dir=/usr/local \
        --enable-opcache \
        --enable-maintainer-zts
        
     
