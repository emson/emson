--- 
layout: post
title: Named Pipes and TCP/IP Connection Strings
wordpress_id: 5
wordpress_url: http://blog.emson.co.uk/?p=5
---
#Named Pipes and TCP/IP Connection Strings

##Manually Creating a SQL Connection String

There are many different connection strings for connecting to a SQLServer database for C#.  Have a look at this web site [http://www.connectionstrings.com/?carrier=sqlserver](http://www.connectionstrings.com/?carrier=sqlserver) for examples.

I needed to create a connection to a SQLServer and started using this as my connection string:

	server=myServer;database=myDatabase;uid=someUser;pwd=somePassword

However I got the following error:

	An error has occurred while establishing a connection to the server. 
	When connecting to SQL Server 2005, this failure may be caused by
	the fact that under the default settings SQL Server does not allow 
	remote connections. (provider: Named Pipes Provider, error: 40 - 
	Could not open a connection to SQL Server)

Pipes are actually quite an old technology, but what are they?

<!--more-->

##What are Named Pipes on Windows

[MSDN](http://msdn.microsoft.com/en-us/library/aa365590.aspx) defines a pipe as:

>  A named pipe is a named, one-way or duplex pipe for communication between the pipe server and one or more pipe clients. 

Pipes are like sockets and allow peer-to-peer communication.  Additionally any process can act as either a server or a client.
Pipes can be accessed remotely but you can also deny access to it from other networked authorities.

Really we should use TCP/IP for our database connections and initially I thought I had to create a special connection string defining TCP/IP settings.

Again as I looked into the problem more closely it turned out that I was again connecting to a server that was not running SQLServer.
Anyway I am now using the following connection string:

	Data Source=myServer;Database=myDatabase;User ID=someUser;Password=somePassword;




