--- 
layout: post
title: How to determine whether a column exists on SQLServer
wordpress_id: 11
wordpress_url: http://blog.emson.co.uk/2008/06/how-to-determine-whether-a-column-exists-on-sqlserver/
---
#How to determine whether a column exists on SQLServer
I run some SQL scripts against Microsoft's SQLServer in a number of environments, test, staging and production.

One of my problems is that these scripts alter the database schema and I only want them to execute if the change hasn't already been made.

One of these changes was to remove a column from the database, so my question is 'how can I ask SQLServer whether a particular column exists'.

Well the SQL query to check if a column exists and execute a query if it does is as follows:
{% highlight sql %}
IF EXISTS ( SELECT * FROM INFORMATION_SCHEMA.COLUMNS
                 WHERE TABLE_NAME='my_table_name' AND
                 COLUMN_NAME='my_column_name' )
BEGIN
  PRINT 'Execute some query here'
END
{% endhighlight %}
