---
layout: post
title: Installing MariaDB using Homebrew
published: true
categories: database, mysql, mariadb
---

# Installing MariaDB using Homebrew


[MariaDB](http://mariadb.org/) is a binary compatible and improved version of [MySQL](http://www.mysql.com/). This means that MariaDB is
just a drop in replacement for MySQL. Why would you use MariaDB over
MySQL? 

Well it's faster, and the speed in which the team fixes bugs and
provides new features is also faster.  Additionally there is talk that
there has been a developmental exodus from the MySQL development team to
MariaDB.

Anyway it is likely to expect MariaDB to overtake MySQL in the next few years
and become the default web database choice and for MySQL to gradually step back from the
limelight.

So this post briefly describes how you can install MariaDB on a Mac
using [Homebrew](http://mxcl.github.com/homebrew/).

## Installation - MariaDB

### Clean up any old MariaDB/MySQL versions

Firstly ensure that you have removed all traces of any previous MariaDB
or MySQL installations. This includes the log directories - so go ahead and check to
see if directories like the one below still exists:

* `/usr/local/var/mysql/`

** Especially if you are getting error messages like this: **  
`mysqld: File '/usr/local/var/mysql/aria_log_control' not found
(Errcode: 13)`  
This is most likely due to your permissions not being set up correctly.

### Steps to install MariaDB

1. Brew install  
From your terminal execute the following commands:

    	brew install mariadb

2. Build the default databases

    	unset TMPDIR
    	mysql_install_db --user=mysql --basedir=$(brew --prefix mariadb)

3. Path settings  
Make sure that you have the following settings in your `.bash_profile`
file:

    	PATH="/usr/local/bin:/usr/local/sbin:$PATH" # if not already present

4. Start the server  
You should now be able to start the MariaDB server:

    	sudo mysql.server start

	And stop it with:

    	sudo mysql.server stop

	Note you can also start it in safe mode with:

    	sudo mysqld_safe

## Nice Little Extras
### Building the MySQL2 Ruby Gem

If you wish to run MariaDB with [Ruby on Rails](http://rubyonrails.org/) then you will want to
compile the standard MySQL2 Gem.  In order to do this it is best to set
your `ARCHFLAGS` for your Mac's processor architecture.  
In this case I am using 64 bit processor.:

		export ARCHFLAGS="-arch x86_64"

Now for the MariaDB specific commands:

		sudo gem install --no-rdoc --no-ri mysql2 -- --with-mysql-dir=$(brew --prefix mariadb) --with-mysql-config=$(brew --prefix mariadb)/bin/mysql_config 

With any luck this should all install fine.

### Configuring for Speed

If you don't tune MySQL and leave default settings, performance will be terrible with production data. Make sure to apply this `cnf` before importing any data or creating a database.

Here is a sample `/etc/my.cnf` (good if you have 3GB+ RAM)

    [mysqld]
    thread_concurrency = 16
    log-bin=mysql-bin
    binlog_format=mixed
    innodb_data_file_path = ibdata1:100M:autoextend
    innodb_buffer_pool_size = 1024M
    innodb_additional_mem_pool_size = 20M
    innodb_log_file_size = 256M
    innodb_log_buffer_size = 32M
    innodb_flush_log_at_trx_commit = 2

    [mysqldump]
    quick
    max_allowed_packet = 16M

    [mysql]
    no-auto-rehash

## References

* [MariaDB](http://mariadb.org/)
* [MySQL](http://www.mysql.com/)
* [Installing system tables
  (mysql_install_db)](http://kb.askmonty.org/en/installing-system-tables-mysql_install_db)
* [Building MariaDB on a Mac OS X using
  Homebrew](http://kb.askmonty.org/en/building-mariadb-on-mac-os-x-using-homebrew)
* [Homebrew](http://mxcl.github.com/homebrew/)
* [Ruby on Rails](http://rubyonrails.org/)
