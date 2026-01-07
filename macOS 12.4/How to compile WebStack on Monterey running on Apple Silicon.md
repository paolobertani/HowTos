#How to compile PHP-Nginx WebStack on macOS 12.4 Monterey [Apple Silicon]
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
/bin
/usr/sbin
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

As a newer SQL client will be used it should be possible to switch to **sha256 authentication** by editing the program argument in the **LaunchDaemon**

Further configuration options are availale in the **System preferences panel**

mySql daemon starts at boot by default

-

###cpdf

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
export CFLAGS="-arch arm64 -g -pipe -no-cpp-precomp -O3 -fno-common -D_DARWIN_C_SOURCE"
export CCFLAGS="-arch arm64 -g -Os -pipe -O3 -D_DARWIN_C_SOURCE"
export CPPFLAGS="-D_DARWIN_C_SOURCE"
export CXXFLAGS="-arch arm64 -g -Os -pipe -O3 -fno-common -D_DARWIN_C_SOURCE"
export LDFLAGS="-Wl,-dead_strip"
export OPENSSLDIR="/usr/local/openssl/"
```

-

### Compiling tools and libs (below)

Download the latest stable version from the websites provided.

Unpack/unzip and move to the root of the unpacked source with `cd /path/to/source`

Then issue the commands specified.

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

browse to the git repository and download **icu4c-64_2-src.zip**

```
cd source
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

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

-

### openssl

**<1>** Download

from **<https://www.openssl.org>**

then **<https://github.com/openssl/openssl/releases>**

download **openssl-3.6.0.tar.gz** (source code)

build **`cd openssl-3.6.0`**

**<2>* Run "Configure"

```
./Configure darwin64-arm64-cc \
  --prefix=/usr/local/openssl \
  --openssldir=/usr/local/openssl \
  no-ssl3 \
  no-zlib \
  enable-ec_nistp_64_gcc_128
```

•	darwin64-arm64-cc → Apple Silicon

•	no-ssl3 → obsolete

•	no-zlib → optional (enable if needed)

**<3>** Compile and Install

```
make -j 10
sudo make install
```

**<4>** Verify

```
/usr/local/openssl/bin/openssl version -a
```

Should read

"OpenSSL 3.6.0 1 Oct 2025 (Library: OpenSSL 3.6.0 1 Oct 2025)"

**<5>** Make it visible to other software (IMPORTANT)

Environment variables

Add to your shell: `touch ~/.zshrc`, `mate ~/.zshrc`



```
export PATH="/usr/local/openssl/bin:$PATH"
export LDFLAGS="-L/usr/local/openssl/lib"
export CPPFLAGS="-I/usr/local/openssl/include"
export PKG_CONFIG_PATH="/usr/local/openssl/lib/pkgconfig"
```

**<6>** Reload shell

and re-set env. variables

**<7>** Verify headers and libs (if pkg-config is available)

```
pkg-config --modversion openssl
```

**<8>** ...Using OpenSSL when compiling other software

```
./configure \
  --with-openssl=/usr/local/openssl \
  --with-openssl-dir=/usr/local/openssl
```

If configure can’t find OpenSSL:

```	
export OPENSSL_ROOT_DIR=/usr/local/openssl
```



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

### curl

from **<https://curl.haxx.se>**

```
./configure --prefix=/usr/local \
  --with-ssl=/usr/local \
  --with-zlib=/usr/local
  
  make -j 10
  
  sudo make install
```

-

### iconv (libiconv)

from <**https://www.gnu.org/software/libiconv/**>

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
./configure --prefix=/usr/local --with-internal-glib
make -j 10
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

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```

### automake

from **<https://ftp.gnu.org/gnu/automake/>**

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




### php

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
   --enable-mbregex \
  --enable-mbstring \
  --enable-opcache \
  --enable-pdo \
  --enable-pcntl \
  --enable-shmop \
  --enable-sockets \
  --without-pcre-jit \
  --with-zlib \
  --with-zlib-dir=/usr/local \
  --with-zip \
  --with-bz2=/usr/local/lib \
  --with-curl=/usr/local \
  --enable-gd \
  --with-jpeg \
  --with-freetype \
  --with-iconv=/usr/local \
  --with-mhash \
  --with-mysql-sock=/tmp/mysql.sock \
  --with-pdo-mysql \
  --with-mysqli \
  --with-openssl=/usr/local \
  --with-external-pcre=/usr/local \
  --with-snmp=/usr \
  --with-xsl=/usr/local \
  --with-zlib=/usr/local
```



```
make -j 10

sudo make install
```

Setup a `php.ini` configuration file:

```
sudo cp php.ini-production /usr/local/php/etc/php.ini
``` 

-


### redis

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

### pear (+ pecl) (go-pear.phar)

download from **<https://pear.php.net/go-pear.phar>**

```
sudo php go-pear.phar
```

at the interactive prompt just hit **enter**

-


### phpredis module (redis-5.\*.\*.tgz)

download from **<https://pecl.php.net/package/redis>**

See also <https://github.com/phpredis/phpredis/>

don't expand the archive

move to the directory that contains the archive then

```
sudo pecl install redis-5.3.7.tgz
```

at the interactive prompt just hit **enter**

*Ensure to avoid confusion between Redis server source and Redis php module: the tarballs have the same name except for the version number*

edit the `php.ini` file adding `extension=redis.so` at the end

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
    --with-pcre=../pcre2-10.40 \
    --with-http_v2_module \
    --add-module=../headers-more-nginx-module-0.33

$ make

$ sudo make install
```


-

### ghostscript

tool for working and fixing pdfs

from **<https://www.ghostscript.com>**

Postscript and PDF interpreter/renderer > Ghostscript > Ghostscript Source > Ghostscript AGPL Release

```
./configure --prefix=/usr/local
make -j 10
sudo make install
```


-

### ttf2pt1

tool for converting TrueType fonts to Type1 fonts

-

`cd` the `fixed+configured` dir; The tool is old and don't receive updates so it's fine to use the downloaded, fixed and configured dir.

```
make -j 10
sudo make install
```
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

### Ruby

from **<https://www.ruby-lang.org/en/downloads/>**

```
configure --prefix=/usr/local/ruby
make -j 10
sudo make install
```

add to `.zpath` 

`/usr/local/ruby/bin`

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

??? sudo ldconfig /usr/local/lib
```
