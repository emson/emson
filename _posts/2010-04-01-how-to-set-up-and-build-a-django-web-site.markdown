--- 
layout: post
title: How To Set Up and Build a Django Web Site
wordpress_id: 84
wordpress_url: http://blog.emson.co.uk/?p=84
---
This article outlines how to set up your new Django site from scratch. I tend to use [Dreamhost Web Site Hosting](http://www.dreamhost.com/r.cgi?105422) so there will be a few notes specific to them.

If you are unsure how to set up Python and Django using [Dreamhost](http://www.dreamhost.com/r.cgi?105422) then checkout my previous article.

I hope you find it useful and drop me a comment if you have any questions. 

##Create the new site

Create a project directory

    $ mkdir -p mysite-project/mysite.com
    $ cd mysite-project/mysite.com
    
Create you new site:

    $ django-admin.py startproject mysite

Now create the following directories:
    
    $ mkdir -p mysite-project/settings
    $ mkdir -p mysite-project/mysite.com/tmp/
    $ mkdir -p mysite-project/mysite.com/public/appmedia
    $ mkdir -p mysite-project/mysite.com/mysite/scripts
    $ mkdir -p mysite-project/mysite.com/mysite/templates


##Initialise git

    $ cd mysite-project/mysite.com
    $ git init
    $ touch .gitignore
    
    Add the following:
    
    .DS_Store
    *.pyc
    packages/
    work/


##Add WSGI file

The following should be carried out from this directory:

    $ cd mysite-project/mysite.com
    
Add the **passenger_wsgi.py** file to mysite-project/mysite.com directory:

    
    import sys, os
    if sys.hexversion < 0x2060000: os.execl("/home/myuser/opt/python261/bin/python2.6", "python2.6", *sys.argv)

    sys.path.append(os.getcwd())
    # START
    # required if you are using VirtualEnv, to get Passenger to use local Python environment.
    # see: http://wiki.dreamhost.com/Passenger_WSGI
    INTERP = "/home/myuser/virtual/bin/python2.6"
    if sys.executable != INTERP: os.execl(INTERP, INTERP, *sys.argv)
    # END
    os.environ['DJANGO_SETTINGS_MODULE'] = "mysite.settings"
    import django.core.handlers.wsgi
    application = django.core.handlers.wsgi.WSGIHandler()

Create the public/appmedia directory in this directory to.
Create tmp/restart.txt


##Set up the VirtualEnv Environment

Move to the mysite-project directory.
Ensure that you have virtualenv installed on your local system: 

    $ cd mysite-project
    $ pip install virtualenv

Create the **virtual/** directories using virtualenv:
    
    $ virtualenv ./virtual
    
Create a symbolic link to the **activate** executable.

    $ ln -s ./virtual/bin/activate ./activate

NB: if you rename your mysite-project directory you may well need to delete the virtual directory and activate symbolic link and recreate them using the previous virtualenv steps. 

You can check which Python site packages being used with this command:

    $ python -c "from distutils.sysconfig import get_python_lib; print get_python_lib()"

##Create Local Dev Database

Change directory to the scripts directory in mysite-project/mysite.com/mysite:

    $ cd mysite-project/mysite.com/mysite/scripts
    $ touch scripts/create-db.sql
    $ touch scripts/drop-db.sql
    
create-db.sql

    create database mysite_com_dev;
    grant all on mysite_com_dev.* to 'root'@'localhost';
    
drop-db.sql

    drop database mysite_com_dev;

Now create the database

    $ mysql -u root < scripts/create-db.sql

##Set up the Django Project Files

Make the following changes to the **settings.py** file.
{% highlight python %}
    import sys
    import os
    import os.path
    import socket

    PROJECT_ROOT = os.path.realpath(os.path.dirname(__file__))
    sys.path.insert(0, os.path.join(PROJECT_ROOT)) # needed because of shared hosting PYTHONPATH problems

    # MEDIA_ROOT = os.path.join(PROJECT_ROOT, 'public/appmedia/')
    MEDIA_ROOT = '/full/local/path/to/appmedia/'
    # MEDIA_URL = '/appmedia/'
    MEDIA_URL = 'http://media.mysite.com/appmedia/'
    ADMIN_MEDIA_PREFIX = 'http://media.mysite.com/media/'
    
    TEMPLATE_DIRS = ( os.path.join(PROJECT_ROOT, 'templates').replace('\\','/'), )
    
    INSTALLED_APPS = (
        'django.contrib.admin', # add this
        'django.contrib.admindocs', # and this
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
    )
    
    # Add this to the bottom to import your local settings
    try:
        from settings_local import *
    except ImportError:
        pass
{% endhighlight %}

Now we need to create a settings_local.py file in the same directory as the main settings.py file:

    $ touch settings_local.py

Then add your default settings (most probably your dev settings):
{% highlight python %}

    DATABASE_ENGINE = 'mysql'     
    DATABASE_NAME = 'mysite_com_dev'
    DATABASE_USER = 'root'         
    DATABASE_PASSWORD = ''         
    DATABASE_HOST = 'localhost'    
    DATABASE_PORT = ''
    
{% endhighlight %}

As we will be using multiple environments we need to have different configurations for each environment.  There are a number of ways to do this, however the DRYist and easiest is to have a main settings file that imports different settings according to the environment.

Change directory to the mysite-project/settings directory, and create the appropriate settings files - we will have a dev, staging and production file as well as the main one:

    $ cd mysite-project/settings
    $ touch settings_dev.py; touch settings_staging.py; touch settings_production.py

Now add the appropriate settings to these files database settings. Note that these settings will overwrite the settings in the main settings file.


##Adding South to Your Application

For detailed installation instructions see [http://south.aeracode.org/wiki/Download](http://south.aeracode.org/wiki/Download).

Note, for MySQL databases, you should install South to a new instance of your MySQL database and not an existing or legacy database. This is because South won't update it's self when you run: `python manage.py migrate`

Quick installation.

Add **South** (Note the capital 'S') to your pip requirements.txt file, and pip install it:

      pip install -r requirements.txt

Or just run:

      pip install South
    
Install the **south** (Note lowercase 's') into your project settings.py file. e.g.:
{% highlight python %}

      INSTALLED_APPS = (
          'django.contrib.admin',
          'django.contrib.admindocs',
          'django.contrib.auth',
          'django.contrib.contenttypes',
          'django.contrib.sessions',
          'django.contrib.sites',
          'south',
      )
      
{% endhighlight %}

Now **syncdb** with your database so that your App knows about South.

      $ python manage.py syncdb

You should get an output similar to this:

          Syncing...
          Creating table south_migrationhistory

          Synced:
           > django.contrib.admin
           > django.contrib.auth
           > django.contrib.contenttypes
           > django.contrib.sessions
           > django.contrib.sites
           > django.contrib.markup
           > south
           > tagging

          Not synced (use migrations):
           - 
          (use ./manage.py migrate to migrate these)

Finally you need to tell South what Apps you want it to manage (in terms of migrations). So for example say you have an App called 'firepages', then run:

      python manage.py startmigration firepages --initial
      
This will create a migrations directory and will allow South to manage all the models in that App:

      Creating migrations directory at '/mypath/mysite-project/mysite.com/mysite/firepages/migrations'...
      Creating __init__.py in '/mypath/mysite-project/mysite.com/mysite/firepages/migrations'...
       + Added model 'firepages.Group'
       + Added model 'firepages.Category'
       + Added model 'firepages.Page'
      Created 0001_initial.py.

Note that because you will be uploading this new migrations directory you shouldn't need to run this command on the server.

You will now need to execute the following command to migrate forward:

      python manage.py migrate

Now if you want to change your database do the following:

1. Modify your model. e.g. add / remove fields
2. Tell South to create a migration: `python manage.py startmigration myapp name-of-my-migration --auto`
3. Execute the migration: `python manage.py migrate`


    
##Change urls.py

Use the following imports:
{% highlight python %}

    from django.conf import settings
    from django.contrib import admin

    admin.autodiscover()
    
{% endhighlight %}

At the bottom add a new URL path so that the site can get access to the appmedia, NB don't forget to add a staging and production version to:
{% highlight python %}

    # simple trick to use local css files for development.
    # (Just make sure DEBUG=False in production!!!)
    if settings.DEBUG:
        urlpatterns += patterns('', 
            url(r'^appmedia/(?P<path>.*)$', 'django.views.static.serve', { 
                'document_root': settings.MEDIA_ROOT }),
            )
{% endhighlight %}    

Now add the following URLs:
{% highlight python %}

    urlpatterns = patterns('',
        #Admin URLS
        # (r'^admin/doc/', include('django.contrib.admindocs.urls')),
        (r'^admin/', include(admin.site.urls)),
        
        #Home URL
        (r'^$', include('mysite.foo.urls'),
    )
{% endhighlight %}

You may want to change the **admin** URL pattern to be something else. This will make the admin area more obscure thus helping to prevent hacking. e.g. **mylogin**


##Add a views.py file

In the mysite-production/mysite.com/mysite/ directory add a views.py file with the following:
{% highlight python %}

    from django.http import HttpResponse

    import settings

    def hello(request, path='none'):
        """quick hello test"""
        return HttpResponse("Quick test, PROJECT_ROOT: %s<br/>URL path: %s" % (settings.PROJECT_ROOT, path))

{% endhighlight %}

##Sync the DB and Start the Server

Navigate to the mysite-project/mysite.com/mysite directory

Start the virtual environment:

    $ . ../../activate
    (virtual)$ python manage.py syncdb
    ** create a user **
    (virtual)$ python manage.py runserver
    
Now open a browser and point it at this URL:

    $ open http://localhost:8000
    
It should display your **hello** view results.


##Requirements

Add a requirements.txt file to the following directory:

    $ touch mysite-project/mysite/requirements.txt
    
Add in any requirements e.g. django-tagging

    echo mysite-project/mysite/requirements.txt << django-tagging

Execute and add requirements:

    pip install -r requirements.txt 


##Local Apps Directory

Create an **apps** directory in the root of your project folder. Then tell your settings.py file about it:

      sys.path.insert(0, os.path.join(PROJECT_ROOT, "apps"))

Now any apps that you want in here and not in your "pip install" directory just add them and they will be found.



##RequestContext on All Views

All your views should implement a RequestContext variable, but first you will need to add the following CONTEXT_PROCESSORS to the settings.py file:
{% highlight python %}

    TEMPLATE_CONTEXT_PROCESSORS = (
        'django.core.context_processors.auth',
        'django.core.context_processors.debug', # passes the DEBUG variable to the template
        'django.core.context_processors.i18n',
        'django.core.context_processors.media', # passes the MEDIA_URL to the template
        'django.core.context_processors.request', # passes the request object to the template
    )
{% endhighlight %}

Now Make sure that you pass the RequestContext to the render.
{% highlight python %}

    def home_page(request):
        """display home page"""
        template = "page_home.html" 
        return render_to_response(template, { 'foo':'bar' }, context_instance=RequestContext(request))
{% endhighlight %}

This will allow you to use generic templates, and also get a handle on certain variables in the template.


##Page Specific Menus

See this article for more information:  [http://gnuvince.wordpress.com/2007/09/14/a-django-template-tag-for-the-current-active-page/](http://gnuvince.wordpress.com/2007/09/14/a-django-template-tag-for-the-current-active-page/)


Django doesn't out of the box provide a **current_page** variable like Ruby on Rails - but it is easy enough to implement.

You will need to have the **'django.core.context_processors.request',** setting set in the template context processors as specified above.

You will also need to create a module called **templatetags** with a __init__.py file in it.
Now add a file called **active_menus.py**
{% highlight python %}

        from django import template

        register = template.Library()

        @register.simple_tag
        def link_to(request, pattern, url, name):
            import re
            if re.search(pattern, request.path):
                return '<span class="active">%s</span>' % name
            return '<a href="%s">%s</a>' % (url, name)
            
{% endhighlight %}

Now in your template *load* this template tag and call it like this:
{% highlight django %}

        {% load active_menus %}
        <li>{% link_to request "^/$" "/" "home" %}</li>
        <li>{% link_to request "^/software/$" "/software/" "software" %}</li>
        <li>{% link_to request "^/books/$" "/books/" "books" %}</li>       
{% endhighlight %}

##Template Includes

When creating an include template file give it a name starting with an underscore e.g. `_nav.html`


##Add a Sitemap to Your Site

Add the following to your settings.py file in the INSTALLED_APPS section:

        django.contrib.sitemaps

Now check that you have the following installed also.

This should be in the TEMPLATE_LOADERS:

      'django.template.loaders.app_directories.load_template_source',

Make sure youâ€™ve installed the sites framework. Also make sure that you have set the site domain name and description correctly in the **sites** project in the **Admin Panel**.  It should not be **example.com**, but something like: **myproject.com**

Create a **sitemaps.py** file in your project. This file should contain something like:

{% highlight python %}

from django.contrib.sitemaps import Sitemap
from myproject.models import ProductPage, ProductCategory

class ProductPageSitemap(Sitemap):
    changefreq = "never"
    priority = 0.5

    def items(self):
        return ProductPage.objects.all().filter(published=True)


class ProductCategorySitemap(Sitemap):
    changefreq = "never"
    priority = 0.5

    def items(self):
        return ProductCategory.objects.all()

{% endhighlight %}

Now add the following to your **urls.py** file:
{% highlight python %}
        
        from myproject.sitemaps import *
        # from django.contrib.sitemaps import FlatPageSitemap, GenericSitemap
        
        sitemaps = {
            'product_pages': ProductPageSitemap,
            'product_categories': ProductCategorySitemap,
        }
        
        urlpatterns = patterns('',
          (r'^sitemap\.xml$', 'django.contrib.sitemaps.views.sitemap', {'sitemaps': sitemaps})
        )

{% endhighlight %}

##Adding a new App

    $ python manage.py startapp myappname

Add myappname to your settings.py file INSTALLED_APPS
Now create some models in your myappname/models.py file.

Then run a syncdb:

    $ python manage.py syncdb 
    OR to see the SQL
    $ python manage.py sql myappname

Now if you want to add South migrations:

    $ python manage.py startmigration myappname --initial



##Installing PIL

PIL should not be installed as part of the requirements.txt file, as you will need to compile libjpeg first.
