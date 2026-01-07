#How to compile PHP-Nginx WebStack on macOS 10.14 Mojave
_

### System integrity protection
SIP is left **enabled** 

-

###bash profile

Edit/create `~/.bash_profile` to allow direct invocation of mySql binaries and binaries in `/usr/local/sbin`:

```
export PATH=/usr/local/mysql/bin:/usr/local/sbin:$PATH
```

close the terminal session and open a new one

-

### xCode command line tools

Download and install xCode then enable command line tools with

    xcode-select --install

or use the **command line tools installer**.

-

### Install headers for macOS 10.14

    open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg

-

###mySql

Just use the **installer**

During guided configuration process select **legacy authentication**, user: `root`, password: `cocacola`.

As a newer SQL client will be used it should be possible to switch to **sha256 authentication** by editing the program argument in the **LaunchDaemon**

Further configuration options are availale in the **System preferences panel**

mySql daemon starts at boot by default

-

###Environment variables

```
export KERNEL_BITS=64

export MACOSX_DEPLOYMENT_TARGET=10.14
export CFLAGS="-arch x86_64 -g -pipe -no-cpp-precomp -O3 -fno-common"
export CCFLAGS="-arch x86_64 -g -Os -pipe -O3"
export CXXFLAGS="-arch x86_64 -g -Os -pipe -O3 -fno-common"
export LDFLAGS="-arch x86_64 -bind_at_load"
```

-

###Compiling tools and libs (below)

Download the latest stable version from the websites provided.

Unpack/unzip and move to the root of the unpacked source with `cd /path/to/source`

Then issue the commands specified.

-

###autoconf

from **<https://www.gnu.org/software/autoconf/>**

```
./configure --prefix=/usr/local

make -j 4

sudo make install
```

-

###icu4c

from **<http://site.icu-project.org>**

browse to the git repository and download **icu4c-64_2-src.zip**

```
cd source
./runConfigureICU MacOSX --prefix=/usr/local
make -j 4
sudo make install
```

-

###libjpeg

from **<http://www.ijg.org>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

-

###libpng

from **<http://www.libpng.org>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

-

###pcre2

from **<https://www.pcre.org>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

-

###libmcrypt 2.5.8

from **<https://sourceforge.net/projects/mcrypt/files/Libmcrypt/2.5.8/>**

note that **mcrypt** has been deprecated since PHP 7.2 (in favour of OpenSSL) and can be used only as a PECL extension

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

-

###openssl

from **<https://www.openssl.org>**

```
./Configure darwin64-x86_64-cc
make -j 4
sudo make install
```

OpenSsl is installed by default in `/usr/local`

Libraries are located in `/usr/local/lib`

Executable is located in `/usr/local/bin/`

Try `openssl version` to see check that version is the one just built and **not** LibreSSL

Headers are located in the build directory. Don't delete it as headers are needed later.

-

###freetype

from **<https://www.freetype.org>**

```
./configure --prefix=/usr/local \
  --enable-freetype-config
make -j 4
sudo make install
```

-

###zlib

from **<https://www.zlib.net>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
``` 

-

###curl

from **<https://curl.haxx.se>**

```
./configure --prefix=/usr/local \
  --with-ssl=/usr/local \
  --with-zlib=/usr/local
  
  make -j 4
  
  sudo make install
```

-

###iconv

from **<https://www.gnu.org/software/libiconv/**>

```
./configure --prefix=/usr/local
make -j 4
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
make -j 4
sudo make install
```

###bison

from **<https://www.gnu.org/software/bison/>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

Check the bison just compiled is the one used

```
bison -V
```

###re2c

from **<http://re2c.org>**

```
./configure --prefix=/usr/local
make -j 4
sudo make install
```

Check the re2c just compiled is the one used

```
re2c -v
```

###php

from **<https://www.php.net/downloads.php>**

```
./configure \
  --prefix=/usr/local \
  --sysconfdir=/usr/local/php/etc \
  --with-config-file-path=/usr/local/php/etc \
  --infodir=/usr/local/share/info \
  --mandir=/usr/local/share/man \
  --enable-bcmath \
  --enable-cli \
  --enable-dom \
  --enable-exif \
  --enable-fileinfo \
  --enable-fpm \
  --enable-ftp \
  --enable-intl \
  --enable-json \
  --enable-mbregex \
  --enable-mbstring \
  --enable-opcache \
  --enable-pdo \
  --enable-pcntl \
  --enable-shmop \
  --enable-sockets \
  --enable-zip \
  --with-curl=/usr/local \
  --with-freetype-dir=/usr/local/ \
  --with-gd \
  --with-icu-dir=/usr/local \
  --with-iconv=/usr/local \
  --with-iconv-dir=/usr/local \
  --with-jpeg-dir=/usr/local \
  --with-mhash \
  --with-mysql-sock=/tmp/mysql.sock \
  --with-pdo-mysql \
  --with-mysqli \
  --with-openssl=/usr/local \
  --with-pcre-regex=/usr/local \
  --with-png-dir=/usr/local \
  --with-snmp=/usr \
  --with-xmlrpc \
  --with-xsl=/usr \
  --with-zlib=/usr/local
```

before compiling be aware of this **<https://stackoverflow.com/questions/57734434/libiconv-or-iconv-undefined-symbol-on-mac-osx/57734435>**

open the **Makefile** with `mate Makefile`

Search lines with `MacOSX.sdk` occurrencies

Move the **path to xCode's libraries** at the end of each line so that those are the last choice.

Edit `INCLUDES =` adding `-I/Users/administrator/Desktop/WebStack/libiconv-1.16/include` as second element. 

Edit `EXTRA_LIBS` substituting `-lincov` with `/usr/local/lib/libiconv.dylib`

This will avoid `iconv` related compiling errors.

```
make -j 4

sudo make install
```

Setup a `php.ini` configuration file:

```
sudo cp php.ini-production /usr/local/php/etc/php.ini
``` 

-

###mcrypt php module

download from **<https://pecl.php.net/package/mcrypt>**

don't expand the archive

move to the directory that contains the archive then

```
sudo pecl install mcrypt-1.0.2.tar
```

edit the `php.ini` file adding `extension=mcrypt.so` at the end

-

###redis

from **<https://redis.io/download>**

open a new terminal window, don't set environment variables

```
make
sudo make PREFIX=/usr/local/redis install
sudo mkdir /usr/local/redis/etc
sudo cp redis.conf /usr/local/redis/etc
sudo ln -s /usr/local/redis/bin/redis-server /usr/local/bin/redis-server 
sudo ln -s /usr/local/redis/bin/redis-cli /usr/local/bin/redis-cli 
```

config file is `/usr/local/redis/etc/redis.conf`; Redis server can be started with `sudo redis-server`

**Set environment variables again.**

-

###phpredis module

download from **<https://pecl.php.net/package/redis>**

See also <https://github.com/phpredis/phpredis/>

don't expand the archive

move to the directory that contains the archive then

```
sudo pecl install redis-5.0.2.tar
```

Ensure to avoid confusion between Redis server source and Redis php module: the tarballs have the same name except for the version number

edit the `php.ini` file adding `extension=redis.so` at the end

-

###nginx headers-more

from **<https://github.com/openresty/headers-more-nginx-module/tags>**

get the latest release and just unpack the tarball

-

###pcre (for nginx)

from **<https://www.pcre.org>**

just download and unpack the tarball

-

###nginx

from **<https://nginx.org/>**

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
    --with-pcre=../pcre-8.43 \
    --with-http_v2_module \
    --add-module=../headers-more-nginx-module-0.33

$ make

$ sudo make install
```
-





 

...also installed

automake-1.16
binutils-2.37.90libtool-2.4.6