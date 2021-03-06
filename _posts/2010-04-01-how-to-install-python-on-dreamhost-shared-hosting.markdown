--- 
layout: post
title: How to Install Python on Dreamhost Shared Hosting
wordpress_id: 80
wordpress_url: http://blog.emson.co.uk/?p=80
---
#How to Install Python on Dreamhost Shared Hosting

This article describes how to install and set up Python and VirtualEnv on [Dreamhost web site hosting](http://www.dreamhost.com/r.cgi?105422).

Some of these notes are based on this article: 

URL:  [http://blog.localkinegrinds.com/2007/08/20/custom-python-installation-for-django-on-dreamhost/](http://blog.localkinegrinds.com/2007/08/20/custom-python-installation-for-django-on-dreamhost/)


##Set up

Log into your shared server and do the following:
{% highlight bash %}

$ mkdir -p opt/python261
$ mkdir downloads
$ cd downloads
$ wget http://www.python.org/ftp/python/2.6.1/Python-2.6.1.tgz
$ tar xvzf Python-2.6.1.tgz

{% endhighlight %}

Note that:
According to the [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/pub/fhs-2.3.html#OPTADDONAPPLICATIONSOFTWAREPACKAGES), the /opt dir "is reserved for the installation of add-on application software packages."


Now configure where your new Python instance will be installed:
{% highlight bash %}
$ cd Python-2.6.1
$ ./configure --prefix=$HOME/opt/python261
$ make
$ make install > install.txt
{% endhighlight %}    

NB: The install.txt file is useful to locate files if you wish to uninstall Python later.
NB: you might wish to use:
{% highlight bash %}
$ ./configure --prefix=$HOME/opt/python261  --enable-unicode=ucs4
{% endhighlight %}
    
But Dreamhost does uses ucs2 by default (Python's default to), and there can be issues if you try to load packages that are compiled using a different Unicode setting.


Now edit your ~/.bash_profile file to specify the new Python install located in: ~/opt/
{% highlight bash %}
export PATH=$PATH:$HOME/opt/python261/bin
{% endhighlight %}
    
You might also want to add an alias so that instead of typing **python2.6** to access the python shell, you can just type **python** in the command line.
{% highlight bash %}
alias python='python2.6'
{% endhighlight %}
    
Don't forget to tell bash to load this file after you have made your changes:
{% highlight bash %}
$ source ~/.bash_profile
$ python -V  # will display your new version 2.6.1
{% endhighlight %}


##Installing VirtualEnv
{% highlight bash %}
$ cd downloads/
$ curl -O http://pypi.python.org/packages/source/v/virtualenv/virtualenv-1.4.5.tar.gz
$ tar -xvzf virtualenv-1.4.5.tar.gz
$ python virtualenv-1.4.5/virtualenv.py $HOME/virtual --no-site-packages
{% endhighlight %}

NB: This binds your new install with virtualenv and creates a virtual directory in your user home directory.
You will need to use the **--no-site-packages** option so that virtualenv doesn't use any of the original Dreamhost Python install packages.

Update your **~/.bash_profile** file so that it puts the virtualenv packages onto the Python path, and *activate(s)* your virtual environment:
{% highlight bash %}
export PATH="$PATH:$HOME/opt/python261/bin:$HOME/virtual/bin"
source $HOME/virtual/bin/activate
{% endhighlight %}
    
Check that the packages directory is in your new virtual **site_packages** directory:
{% highlight bash %}
$ python -c "from distutils.sysconfig import get_python_lib; print get_python_lib()"
{% endhighlight %}

Now activate your virtual environment, by executing the **.bash_profile** file:
{% highlight bash %}
$ source ~/.bash_profile
{% endhighlight %}

Or manually activate it by:
{% highlight bash %}
$ . ~/virtual/bin activate
{% endhighlight %}


##Installing MySQL Python Bindings

[http://sourceforge.net/projects/mysql-python/files/mysql-python-test/1.2.3c1/MySQL-python-1.2.3c1.tar.gz/download](http://sourceforge.net/projects/mysql-python/files/mysql-python-test/1.2.3c1/MySQL-python-1.2.3c1.tar.gz/download)
{% highlight bash %}
(virtual)$ cd downloads
(virtual)$ wget http://internap.dl.sourceforge.net/sourceforge/mysql-python/MySQL-python-1.2.3c1.tar.gz
(virtual)$ tar xvzf MySQL-python-1.2.3c1.tar.gz
(virtual)$ cd MySQL-python-1.2.3c1
(virtual)$ python setup.py install
{% endhighlight %}

Check that you can import the database correctly (NB: do this from a different directory from the one where MySQL-python was installed, as you will get Module already imported warnings):
{% highlight bash %}
(virtual)$ cd ~
(virtual)$ python
>>> import MySQLdb
{% endhighlight %}  
    
If you don't get an error all succeeded OK.

##Install Django
{% highlight bash %}
(virtual)$ pip install Django
{% endhighlight %}
