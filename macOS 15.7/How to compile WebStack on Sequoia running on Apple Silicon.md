#How to compile PHP-Nginx WebStack on macOS 15.7 *Sequoia* [Apple Silicon]
_

### System integrity protection
SIP is left **enabled** 

-

### zsh profile

edit `/etc/paths`:

```
/usr/local/mysql/bin
/usr/local/ruby/bin
/usr/local/bin
/usr/local/sbin
/usr/bin
/usr/sbin
/bin
/sbin
/Users/administrator/www/www.pinaxo.com/MacOS
```

-

### xCode command line tools

Download and install xCode then enable command line tools with

    xcode-select --install

or use the **command line tools installer**.

-

### mySql

Just use the **installer**

During guided configuration process select **legacy authentication**, user: `root`, password: `cocacola`.

Select **Use strong password encryption**

Further configuration options are availale in the **System preferences panel**

mySql daemon starts at boot by default

-

### mySql setup

####System Preferences

Stop the server if running

**Configuration file:** `/usr/local/mysql/etc/my.cnf`

**Error Log:** `/usr/local/mysql/log/mysqld.error.log`

**PID File:** `/usr/local/mysql/run/mysqld.local.pid`

Some directories are missing:

```
cd /usr/local/mysql
sudo mkdir log
sudo mkdir run
sudo mkdir etc
```

Those directories must allow `_mysql` user to write and read

```
sudo chown -R _mysql:_mysql /usr/local/mysql/log
sudo chown -R _mysql:_mysql /usr/local/mysql/run
sudo chown -R _mysql:_mysql /usr/local/mysql/etc
```

Mysql will let Pid file be accessible by anyone, log file only by owner.


###configuration

Create this `my.cnf` file into `/usr/local/mysql/etc` and ensure owner is `root:wheel`

```
# For advice on how to change settings please see
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html

[mysqld]

# mysql mode
#
# do not set to use defaults
#
# inspect mode anytime with the query "SELECT @@GLOBAL.sql_mode;"
#
# be advised that chaging some sql modes when databases are already set up
# may result in database corruption or data loss
#
# mysql is considered in "strict mode" when STRICT_TRANS_TABLES or STRICT_ALL_TABLES (or both) are enabled
#
# default:
# sql_mode="ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"

sql_mode="ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"


# affects how the server handles identifier case sensitivity
#
# use 2 on macOS as it's a case-insensitive filesystem:
# Table and database names are stored on disk using the lettercase specified in the CREATE TABLE or CREATE DATABASE statement, but MySQL converts them to lowercase on lookup.
# Name comparisons are not case sensitive.
# InnoDB table names and view names are stored in lowercase, as for lower_case_table_names=1.

lower_case_table_names = 2
```

**mySql** starts as `root`but then the daemons runs as `_mysql`

-

Start mySql from System Preferences

Migrate databases with Navicat Premium

-


### cpdf

Command-line tool for editing PDFs (extracting pages, etc.)

Just use the **installer**

From here: **<https://community.coherentpdf.com>** download the prebuilt command line tool.

Move it in its place

```
sudo cp cpdf /usr/local/bin/cpdf
```

Invoke it to trigger macOS security alert

```
cpdf

```

From System Preferences > Security allow execution then try again


-

### Environment variables

```
export KERNEL_BITS=64

export MACOSX_DEPLOYMENT_TARGET=15.7
export CFLAGS="-arch arm64 -g -pipe -no-cpp-precomp -O3 -fno-common"
export CCFLAGS="-arch arm64 -g -Os -pipe -O3"
export CXXFLAGS="-arch arm64 -g -Os -pipe -O3 -fno-common"
export LDFLAGS="-arch arm64 -bind_at_load -L/usr/local/openssl/lib -L/usr/local/lib"
```

-

### ~/.zshrc

```
mate ~/.zshrc
```

&nbsp;

```
export PATH="/usr/local/openssl/bin:$PATH"
export LDFLAGS="-L/usr/local/openssl/lib -L/usr/local/lib"
export CPPFLAGS="-I/usr/local/openssl/include -I/usr/local/include"
export PKG_CONFIG_PATH="/usr/local/openssl/lib/pkgconfig"
```

-

### General rule when building tools and libs

Download the latest stable version.

Unpack/unzip and `cd /path/to/source` into the source directory

Set environment variables.

Configure, compile and istall.

It is advisable to exclude Webstack directory from Spotlight search

-

### gnu m4 (m4)

from **<https://ftp.gnu.org/gnu/m4/>**

```
./configure --prefix=/usr/local

make -j 10

sudo make install
```

### libtool

from **<https://www.gnu.org/software/libtool/>**

```
./configure --prefix=/usr/local

make -j 10

sudo make install
```



### autoconf

from **<https://www.gnu.org/software/autoconf/>**

```
./configure --prefix=/usr/local

make -j 10

sudo make install
```

-

### icu4c

from **<http://site.icu-project.org>**

browse to the git repository and download **Source Code**

```
cd icu4c/source
./runConfigureICU MacOSX --prefix=/usr/local
make -j 10
sudo make install
```

-

### libjpeg (jpeg-9e)

from **<http://www.ijg.org>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

-

### libpng

from **<http://www.libpng.org>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

-

### pcre2

from **<https://www.pcre.org>**

download "pcre2 ... .tar.gz", not "source code"

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

&nbsp;
copy components in build directory into /usr/local:
&nbsp;


```
sudo ls

sudo cp libpcre2-8.pc /usr/local/lib/pkgconfig/libpcre2-8.pc
sudo cp libpcre2-16.pc /usr/local/lib/pkgconfig/libpcre2-16.pc
sudo cp libpcre2-32.pc /usr/local/lib/pkgconfig/libpcre2-32.pc
sudo cp libpcre2-posix.pc /usr/local/lib/pkgconfig/libpcre2-posix.pc

sudo cp src/pcre2.h /usr/local/include/pcre2.h

sudo cp .libs/libpcre2-8.0.dylib /usr/local/lib/libpcre2-8.0.dylib
sudo cp .libs/libpcre2-8.0.dylib /usr/local/lib/libpcre2-8.dylib

sudo cp .libs/libpcre2-posix.3.dylib /usr/local/lib/libpcre2-posix.dylib
sudo cp .libs/libpcre2-posix.3.dylib /usr/local/lib/libpcre2-posix.dylib


```

-

### openssl

from **<https://www.openssl.org>**

```
./Configure darwin64-arm64
make -j 10
sudo make install
```

OpenSsl is installed by default in `/usr/local`

Libraries are located in `/usr/local/lib`

Executable is located in `/usr/local/bin/`

Try `openssl version` to see check that version is the one just built and **not** LibreSSL - If LibreSSL is displayed open a new terminal session and re-set environment variables.

Headers are located in the build directory. Don't delete it as headers are needed later.

-

### freetype

from **<https://www.freetype.org>**

```
./configure --prefix=/usr/local \
  --enable-freetype-config
make -j 10
sudo make install
```

-

### zlib

from **<https://www.zlib.net>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
``` 

-

### automake

from **<https://ftp.gnu.org/gnu/automake/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

-

### libpsl
from **<https://github.com/rockdaboot/libpsl/releases>**

```
./configure --prefix=/usr/local

make -j 10
sudo make install
```

-

### groff

from **<https://www.gnu.org/software/groff/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```
-

### libldap

from **<https://www.openldap.org/software/download/OpenLDAP/openldap-release/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```
-

### curl

from **<https://curl.haxx.se>**

```
DYLD_LIBRARY_PATH="/usr/local/lib:/usr/local/openssl/lib"

autoreconf -fi # this is needed when downloading the GIT archive

ln -sf /usr/local/lib/libicudata.73.dylib  libicudata.73.dylib
ln -sf /usr/local/lib/libicuuc.73.dylib    libicuuc.73.dylib

mv lib/curl_config.h lib/curl_config_.h

./configure --prefix=/usr/local \
  --with-openssl=/usr/local/openssl \
  --with-zlib=/usr/local \
  --without-libpsl \
  --disable-ldap \
  --disable-ldaps
    
make -j 10
  
sudo make install
```

-

### iconv (libiconv)

from **<https://www.gnu.org/software/libiconv/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

### cmake

Download application from **<https://cmake.org>**

Create symlink to CMake tool

```
sudo ln -s /Applications/CMake.app/Contents/bin/cmake /usr/local/bin/cmake
```

Start a new terminal session and re-enter environment variables

-

### libzip

From **<https://libzip.org>**

```
cmake -DCMAKE_INSTALL_PREFIX=/usr/local
make -j 10
sudo make install
```

-

### bzip

From **<https://sourceware.org/pub/bzip2/>**

```
make -j 10
sudo make install
```

### bison

from **<https://www.gnu.org/software/bison/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

Check the bison just compiled is the one used

```
bison -V
```

normally a different version is run: in case, open a new terminal session an check again

### re2c

from **<http://re2c.org>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

Check the re2c just compiled is the one used

```
re2c -v
```


### pkg-config

from **<http://pkg-config.freedesktop.org/>**

```

export CFLAGS="-Wno-error=int-conversion -Wno-error=incompatible-pointer-types -Wno-error"
export CPPFLAGS="-Wno-error=int-conversion -Wno-error=incompatible-pointer-types -Wno-error"
export CXXFLAGS="-Wno-error=int-conversion -Wno-error=incompatible-pointer-types -Wno-error"

./configure --prefix=/usr/local --with-internal-glib
make
sudo make install
```

### libxml (libxml2)

from **<http://xmlsoft.org/download/>**

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

### libxslt

from **<http://xmlsoft.org/download/>**

```

export LIBXML_LIBS="-l/usr/local/lib/libxml2.dylib"
export LIBXML_CFLAGS="-arch arm64 -g -pipe -no-cpp-precomp"

./configure --prefix=/usr/local \
            --with-libxml-prefix=/usr/local \
            --with-libxml-include_prefix=/usr/local/include \
            --with-libxml-libs_prefix=/usr/local/lib \
            --with-libxml-src=/Users/administrator/WebStack/libxml2-2.9.12

make -j 10
sudo make install
```

Open a new terminal window and re-set environment variables


### sqlite3

from **<https://www.sqlite.org/download.html>**

download sqlite autoconf

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

### oniguruma

from **<https://github.com/kkos/oniguruma>**

```
autoreconf -vfi
./configure --prefix=/usr/local
make -j 10
sudo make install
```




### php *8.5.x*



from **<https://www.php.net/downloads.php>**

building on Sonoma:
...`-with-icu-dir` is not recognized and icu libs are not found so make symlinks on the build dir:

```
cd /path/to/php
ln -s /usr/local/lib/libicudata.73.dylib libicudata.73.dylib
ln -s /usr/local/lib/libicui18n.73.dylib libicui18n.73.dylib
ln -s /usr/local/lib/libicuio.73.dylib libicuio.73.dylib
ln -s /usr/local/lib/libicutest.73.dylib libicutest.73.dylib
ln -s /usr/local/lib/libicutu.73.dylib libicutu.73.dylib
ln -s /usr/local/lib/libicuuc.73.dylib libicuuc.73.dylib
```

&nbsp;

run `./configure --help` to display flags and configure parameters

&nbsp;

```
./configure \
  --prefix=/usr/local \
  --sysconfdir=/usr/local/php/etc \
  --with-config-file-path=/usr/local/php/etc \
  --infodir=/usr/local/share/info \
  --mandir=/usr/local/share/man \
  --enable-sysvsem --enable-sysvshm --enable-shmop \
  --enable-bcmath \
  --enable-cli \
  --enable-dom \
  --enable-exif \
  --enable-fileinfo \
  --enable-fpm \
  --enable-ftp \
  --enable-intl \
  --enable-mbregex \
  --enable-mbstring \
  --enable-opcache \
  --enable-pdo \
  --enable-pcntl \
  --enable-sockets \
  --enable-xdebug \
  --enable-gd \
  --without-pcre-jit \
  --with-zlib \
  --with-zlib-dir=/usr/local \
  --with-zip \
  --with-bz2=/usr/local/lib \
  --with-curl=/usr/local \
  --with-jpeg \
  --with-freetype \
  --with-iconv=/usr/local \
  --with-icu-dir=/usr/local/lib \
  --with-mhash \
  --with-mysql-sock=/tmp/mysql.sock \
  --with-pdo-mysql \
  --with-mysqli \
  --with-openssl=/usr/local/openssl \
  --with-external-pcre=/usr/local \
  --with-snmp=/usr \
  --with-xsl=/usr/local \
  --with-zlib=/usr/local
```

&nbsp;
build:
&nbsp;

```
make -j 10

sudo make install
```

```
cd /usr/local/php

sudo mkdir var
sudo mkdir var/run

cd /usr/local/php/etc

sudo cp <php-fpm.conf> php-fpm.conf

sudo mv php.ini php.ini.default
sudo cp <php.ini> php.ini

sudo cp <cacert.pem> cacert.pem
```

Comment `extension=redis.so` until redis extension is installed

-


### redis

>from **<https://redis.io/download>**
>
>open a new terminal window, don't set environment variables
>
>```
>make
>sudo make PREFIX=/usr/local/redis install
>sudo mkdir /usr/local/redis/etc
>sudo cp redis.conf /usr/local/redis/etc
>sudo ln -s /usr/local/redis/bin/redis-server /usr/local/bin/redis-server 
>sudo ln -s /usr/local/redis/bin/redis-cli /usr/local/bin/redis-cli 
>```
>
>config file is `/usr/local/redis/etc/redis.conf`; Redis server can be started with `sudo redis-server`
>
>**Set environment variables again.**

-

### pear (+ pecl) (go-pear.phar)

download from **<https://pear.php.net/go-pear.phar>**

```
sudo php go-pear.phar
```

at the interactive prompt just hit **enter**

-


### phpredis module (redis-5.\*.\*.tgz)

>download from **<https://pecl.php.net/package/redis>**
>
>See also <https://github.com/phpredis/phpredis/>
>
>don't expand the archive
>
>move to the directory that contains the archive then
>
>```
>sudo pecl install redis-5.3.7.tgz
>```
>
>at the interactive prompt just hit **enter**
>
>*Ensure to avoid confusion between Redis server source and Redis php module: the tarballs have the same name except for the version number*
>
>edit the `php.ini` file adding `extension=redis.so` at the end

-

### nginx headers-more (headers-more-nginx-module)

from **<https://github.com/openresty/headers-more-nginx-module/tags>**

get the latest release and just unpack the tarball


-

### nginx

from **<https://nginx.org/>**

point **pcre** and **headers-more** to the respective directories

```
./configure \
    --prefix=/usr/local \
    --sbin-path=/usr/local/sbin/nginx \
    --conf-path=/usr/local/nginx/nginx.conf \
    --error-log-path=/usr/local/nginx/log/nginx-error.log \
    --pid-path=/usr/local/nginx/var/nginx.pid \
    --lock-path=/usr/local/nginx/var/nginx.lock \
    --user=administrator \
    --group=staff \
    --with-threads \
    --with-http_mp4_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-pcre=../pcre2-10.42 \
    --with-http_v2_module \
    --add-module=../headers-more-nginx-module-0.34

$ make

$ sudo make install
```


-

### ghostscript

tool for working and fixing pdfs

from **<https://www.ghostscript.com>**

Postscript and PDF interpreter/renderer > Ghostscript > Ghostscript Source > Ghostscript AGPL Release

```
export CC=clang
export CXX=clang++

export CFLAGS="-O2 -std=gnu99"
export CXXFLAGS="-O2 -std=gnu++17"

export CPPFLAGS="-I/usr/local/include"
export LDFLAGS="-L/usr/local/lib"
export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig"

./configure --prefix=/usr/local
make -j 10
sudo make install
```


-

>### ttf2pt1
>
>from **<https://ttf2pt1.sourceforge.net>**
>
>tool for converting TrueType fonts to Type1 fonts

>-

>`cd` the `fixed+configured` dir; The tool is old and don't receive updates so it's fine to use the downloaded, fixed and configured dir.

>```
>make -j 10
>sudo make install
>```
-

### pigz

from **<https://zlib.net/pigz/>**

A parallel implementation of gzip for modern multi-processor, multi-core machines

```
make
sudo cp pigz /usr/local/bin/pigz
sudo cp unpigz /usr/local/bin/unpigz
```

-

### libyaml

from **<https://github.com/yaml/libyaml>**

Needed by Ruby

```
./bootstrap
sudo mkdir /usr/local/ruby
make -j 10
sudo make install
```

-

### Ruby v4

from **<https://www.ruby-lang.org/en/downloads/>**

```
unset LDFLAGS
export LDFLAGS="-L/usr/local/openssl/lib -L/usr/local/lib"

./configure --prefix=/usr/local/ruby
make -j 10
sudo make install-nodoc
```

`mate ~/.zshrc` update **PATH** as 

`export PATH="/usr/local/openssl/bin:/usr/local/ruby/bin:/usr/local/php/bin:/usr/local/openssl/nginx:$PATH"`

-

### ImageMagick

**with heic and webp support**

**<https://github.com/strukturag/libde265>**

```
./autogen.sh
./configure
make -j 10
sudo make install
```

**<https://github.com/strukturag/libheif>**

```
./autogen.sh 
./configure --disable-examples
make -j 10
sudo make install
```

**<https://github.com/videolan/x265>**

```
cd source
cmake -DCMAKE_INSTALL_PREFIX=/usr/local
make -j 10
sudo make install
```

**<https://github.com/webmproject/libwebp>**

```
./autogen.sh
./configure
make -j 10
sudo make install
```

**<https://github.com/ImageMagick/ImageMagick/releases/tag/7.1.0-52>**


```
./configure --with-heic=yes --with-webp=yes
make -j 10
sudo make install
```
