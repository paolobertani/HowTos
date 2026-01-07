#How to configure the WebStack on macOS 10.14 Mojave
_

##mySql


###System Preferences

Stop the server if running

**Configuration file:** `/usr/local/mysql/etc/my.cnf`

**Error Log:** `/usr/local/mysql/log/mysqld.error.log`

**PID File:** `/usr/local/mysql/run/mysqld.local.pid`

If any directory is missing `sudo mkdir` it.

###Log and PID files

Their directories must allow `_mysql` user to write and read

```
sudo chown _mysql:_mysql /usr/local/mysql/log
sudo chown _mysql:_mysql /usr/local/mysql/run
sudo chown _mysql:_mysql /usr/local/mysql/etc
```

Mysql will let Pid file be accessible by anyone, log file only by owner.


###configuration

Create this `my.cnf` file

```
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

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

Then

```
sudo mkdir /usr/local/mysql/etc
sudo cp <my.cnf> /usr/local/mysql/etc/my.cnf
```

**mySql** starts as `root`but then the daemons runs as `_mysql`

-

### mySql databases migration

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
cp <webstack.monitor.sh> ~/Scripts/webstack.monitor.sh
cp <backup-pinaxo-db.sh> ~/Scripts/backup-pinaxo-db.sh

sudo cp <com.kalei.webstack.monitor.plist> /Library/LaunchDaemons/com.kalei.webstack.monitor.plist
sudo cp <com.kalei.backup.db.plist> /Library/LaunchDaemons/com.kalei.backup.db.plist
```

**WebStack Monitor** ensure services are up and take care of Pinaxo deployment process (when `html.next` is remotely uploaded).

**backup.db** makes regular backups of the databases. Destination directories need to be created:

```
mkdir /Users/Administrator/backup
mkdir /Users/Administrator/backup/mysql 
```


