--- 
layout: post
title: Everyday MySQL Commands
wordpress_id: 17
wordpress_url: http://blog.emson.co.uk/2008/08/everyday-mysql-commands/
---
#Everyday MySQL Commands
I'm trying to put together a number of blog articles and many of them use the MySQL database, so I figured that I would post up some of my most used everyday MySQL SQL commands.  Most likely I will append further commands as I write more articles so this is page is really a work in progress.

I've also assumed that you want to have full rights when executing these commands so I've used **sudo**, however you may not need this depending on your set up.

Finally if some other web site covers the command better or I'm feeling lazy I'll just pop it in the links list at the bottom of this page.

##Useful MySQL Commands

###Starting MySQL Demon
Start your MySQL database and get it running as a background process:
{% highlight bash %}
sudo mysqld_safe
Ctrl + Z
bg
{% endhighlight %}
###MySQL create databases script
Make a SQL script that creates 3 databases and assigns all rights to my username.
Copy the following SQL and paste it into a file called **create_databases.sql**:
{% highlight sql %}
create database mydb_development;
create database mydb_test;
create database mydb_production;
grant all on mydb_development.* to 'my_username'@'localhost';
grant all on mydb_test.* to 'my_username'@'localhost';
grant all on mydb_production.* to 'my_username'@'localhost' identified by 'my_password';
{% endhighlight %}

###MySQL drop databases script
Make a script to drop databases.
Copy the following SQL and paste it into a file called **drop_databases.sql**:
{% highlight sql %}
drop database nemos_production;
drop database mydb_development;
drop database nemos_test;
{% endhighlight %}

###Executing a script from MySQL
The above scripts can be executed from the command-line with the either of these commands:
{% highlight bash %}
sudo mysql < create_database.sql
or
sudo mysql < drop_database.sql
{% endhighlight %}

###Showing MySQL databases
To check that the databases were created :
{% highlight bash %}
sudo mysqlshow
or
sudo mysql
show databases;
{% endhighlight %}

###Displaying database tables
Displaying the tables of a database:
{% highlight bash %}
sudo mysqlshow mydb_development
or
sudo mysql
show tables from mydb_development;
{% endhighlight %}
    
###Displaying details about a database table
Displaying details about a particular database table:
{% highlight bash %}
sudo mysqlshow mydb_development mytable
{% endhighlight %}
    
###Droping a database table
Dropping a table from a database:
{% highlight bash %}
sudo mysql
use mydb_development;
drop table mytable;
{% endhighlight %}

###Displaying information about a table
Displaying the fields and field metadata about a table:
{% highlight bash %}
show fields from my_table; 
or
desc my_table;
{% endhighlight %}

##Useful Links

 *  [http://openmaniak.com/mysql.php](http://openmaniak.com/mysql.php)
 

These are just a few quick and simple MySQL commands that you will always use.
Hope this helps.
