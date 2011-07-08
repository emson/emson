--- 
layout: post
title: Installing Sqlite3 on Windows for Rails
wordpress_id: 10
wordpress_url: http://blog.emson.co.uk/?p=10
---
#Installing Sqlite3 on Windows for Rails

The Windows version of [Ruby on Rails](http://www.rubyonrails.org) does not ship with the [Sqlite3 database](http://www.sqlite.org), even though the **database.yaml** configuration file is preconfigured to use Sqlite.

This is a *how to* guide on how to install Sqlite3 on your Windows PC.
This article assumes that you already have [Ruby](http://www.ruby-lang.org) and [Ruby on Rails](http://www.rubyonrails.org) installed on your PC.

First you will need to download two files from the Sqlite web site [http://www.sqlite.org/download.html](http://www.sqlite.org/download.html):

    sqlite-3_5_9.zip (214.32 KiB)
    A command-line program for accessing and modifing SQLite databases. 
    See the documentation for additional information.

    sqlitedll-3_5_9.zip (213.17 KiB)
    This is a DLL of the SQLite library without the TCL bindings. 
    The only external dependency is MSVCRT.DLL.

The first file is the Sqlite command line program used for modifing the Sqlite database.  You may or may not use this.

The second file is the Windows DLL library file and Ruby uses this when Rails makes Sqlite database calls.  

When both these ZIP files have been extracted you should have the following files:

*  sqlite3.exe
*  sqlite3.def
*  sqlite3.dll

Copy these file to the **bin** directory of your Ruby installation, if you followed the default Ruby installation it will be located here:

		C:\ruby\bin

Now that you have the Sqlite3 files installed you need to tell Ruby how to use them.  To do this you need to download the Ruby bindings for Sqlite3.<br/>Fortunately this is easy to do, using Ruby gems.  Simply at the command prompt type the following command:

		gem install sqlite3-ruby

You will now need to tell Gems which version you need as you will be presented with the following output:

		Bulk updating Gem source index for: http://gems.rubyforge.org
		Select which gem to install for your platform (i386-mswin32)
		 1. sqlite3-ruby 1.2.2 (mswin32)
		 2. sqlite3-ruby 1.2.2 (ruby)
		 3. sqlite3-ruby 1.2.1 (mswin32)
		 4. sqlite3-ruby 1.2.1 (ruby)
		 5. Skip this gem
		 6. Cancel installation
		>_

Please select option 1, **sqlite3-ruby 1.2.2 (mswin32)**.  All being successful you will get some output like this:

		Successfully installed sqlite3-ruby-1.2.2-mswin32
		Installing ri documentation for sqlite3-ruby-1.2.2-mswin32...
		Installing RDoc documentation for sqlite3-ruby-1.2.2-mswin32...

If you are using Rails 2+ you should be able to run the following rake tasks from your Rails application directory.  For example say you created a Rails application located here: **C:\MyApp** you should be able to execute:

		C:\MyApp>rake db:create
		or
		C:\MyApp>rake db:migrate

Hope this is useful.
