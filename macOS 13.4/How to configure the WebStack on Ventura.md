#How to configure the WebStack on macOS 13.4 Ventura
_


### mySql databases migration without Navicat

Clean events/logs tables for records that can be discarded.

Export table+structure as text file.

**Ensure there are not InnoDB tables with** `FORMAT=FIXED`

Use **HexFiend** to perform search (and eventually replace). Text editors would choke.

-

### migration of wordpress databases

Wordpress (old?) installations use `0000-00-00 00:00:00` as default value for timestamp columns.

This is not allowed as newer mySql runs (by default) with mysql mode `NO_ZERO_DATE` enabled (and we don't want to change this).

Before migrating WP databases check for such fields across all tables and set a real date `2000-01-01 00:00:00` as default value.

-

### issues with views

If a **view** has references to **another view** not yet created an error occurr.

Export **structure only**.

Using a **Text editor** remove the creation of tables.

Finally execute the **structure sql** one or more times until no errors occurr.

-

##nginx

Make directories

```
cd /usr/local/nginx
sudo mkdir sites-available
sudo mkdir sites-enabled
sudo mkdir ssl
```

Copy nginx configuration file

```
sudo cp <nginx.conf> .
```

Copy sites available configuration files

```
cd sites-available

sudo cp <sites-available>/* .
```

Make symlinks into sites enabled pointing to sites available conf. files

```
cd sites-enabled

sudo ln -s /usr/local/nginx/sites-available/00-default.conf 00-default.conf 
sudo ln -s /usr/local/nginx/sites-available/10-pinaxo.conf 10-pinaxo.conf 
sudo ln -s /usr/local/nginx/sites-available/20-blog.pinaxo.conf 20-blog.pinaxo.conf 
sudo ln -s /usr/local/nginx/sites-available/30-assiterm.conf 30-assiterm.conf 
sudo ln -s /usr/local/nginx/sites-available/40-bertanilondon.conf 40-bertanilondon.conf 
sudo ln -s /usr/local/nginx/sites-available/50-viewer.getpixelbook.conf 50-viewer.getpixelbook.conf 
```

Copy certificates

```
cd /usr/local/nginx/ssl

sudo cp <ssl>/* .
```

Create log directories and log files with rw-rw-rw- access (they need to be written by the worker when performing rotation)

```
cd /usr/local/nginx
sudo mkdir log
cd log
sudo mkdir pinaxo.com
sudo mkdir flipits.net

sudo touch pinaxo.com/pinaxo.access.log
sudo touch pinaxo.com/pinaxo.error.log

sudo chmod 666 pinaxo.com/pinaxo.access.log
sudo chmod 666 pinaxo.com/pinaxo.error.log

sudo touch flipits.net/pinaxo.access.log
sudo touch flipits.net/pinaxo.error.log

sudo chmod 666 flipits.net/flipits.net.access.log
sudo chmod 666 flipits.net/flipits.net.error.log
```

Once nginx has g


## redis

Just place the configuration file.
It has not been fine tuned yet.

```
sudo cp <redis.conf> /usr/local/redis/etc/redis.conf
```

### php

```
cd /usr/local/php

sudo mkdir var
sudo mkdir var/run

cd /usr/local/php/etc

sudo cp <php-fpm.conf> php-fpm.conf

sudo mv php.ini php.ini.default
sudo cp <php.ini> php.ini
```

## LaunchDaemons & Scripts

Create a directory `Scripts` into the home folder.

Home folder name is assumed `administrator`

```
cp <webstack.monitor.sh> ~/Scripts/Shell/webstack.monitor.sh

sudo cp <com.kalei.webstack.monitor.plist> /Library/LaunchDaemons/com.kalei.webstack.monitor.plist
```

**WebStack Monitor** ensure services are up and take care of Pinaxo deployment process (when `html.next` is remotely uploaded).

The **Worker** makes regular backups of the databases. Destination directories need to be created:

```
mkdir /Users/Administrator/backup
mkdir /Users/Administrator/backup/mysql 
```


