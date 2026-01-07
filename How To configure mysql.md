# How to configure mySql

&nbsp;

## Installation defaults

`5.6` and `5.7` Mac OS X installation put a `my.cnf` file in `/usr/local/mysql` but this file **is not read** at startup.

In fact

    sudo mysqld --verbose --help | grep -A 1 "Default options"

shows 

    Default options are read from the following files in the given order:
    /etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf 

so file is not one of the files **mysqld** tries to read. None of the file listed exists.

&nbsp;

## Put the file in place

    cd /usr/local/mysql
    sudo mkdir conf
    sudo mv my.cnf conf

&nbsp;

## Edit the file and tune mySql

Here

**http://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html**

are the system variables available.

Various sample configuration files can be found Googling around

What is not specified takes a default value.

To see all the variables and their values (and check values specified in `my.cnf` are actually loaded) run the following query

    SHOW VARIABLES

&nbsp;

## Troubleshot variables not loaded

**mySql** takes variable values from

 - **environment variables**
 - **configuration files** 
 - **parameters passed at launch**

The last prevales over the others.

Check which parameters are passed by the **LaunchDeamon** !

Expecially the error log file, that is hard coded there and if not removed cannot be modified in the `my.cnf` file.
