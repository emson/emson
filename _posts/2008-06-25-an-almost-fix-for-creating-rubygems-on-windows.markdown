--- 
layout: post
title: An Almost Fix for Creating RubyGems on Windows
wordpress_id: 14
wordpress_url: http://blog.emson.co.uk/2008/06/an-almost-fix-for-creating-rubygems-on-windows/
---

##Introduction
I wrote this article originally so that I could write command-line apps using Ruby, as I got further and further down the line I realised that what I really wanted to do was create a command-line app using RubyGems.  I use both Windows and an Apple Mac and I quickly found myself having problems trying to create RubyGems on Windows.

In this article I try to explain my thought process and the problems I encountered.  Unfortunately I had to employ a manual process when trying to package up my project into a *tar* file, as there isn't a simple way to do this in Windows (let me know if you have a solution), however everything else seems to work.

##What's a RubyGem?
One of the joys of Ruby is the [RubyGems](http://www.rubygems.org/) packaging system. 
It is essentially a tool written in Ruby, for packaging up source code for deployment.

The RubyGems site describes it as follows:

> RubyGems is the premier ruby packaging system. It provides:
>
> *  A standard format for destributing Ruby programs and libraries.
> *  An easy to use tool for managing the installation of gem packages.
> *  A gem server utility for serving gems from any machine where RubyGems is installed.



##Why should I create a RubyGem?

Maybe a better question should be *when* should I package my code up into a RubyGem.  Obviously there are no hard and fast rules but if you are going to share your code libraries with others or even with other applications of yours, you should think about using a gem.

Now you might think that you don't want to make all your code public on [RubyForge](http://rubyforge.org/), and there are very good reason not to publish every gem, so you don't have to.
RubyGems comes with its own *local* gem server so you can create your own gem library and share it amoung your other Ruby applications.

This is really quite useful, you get the power of RubyGems without the commitment and maintenance work of making your code OpenSource.  Plus at any point in time you can then make it OpenSource, and live off the joy of the Ruby community :).

The reason I wrote this article is because I wanted to create a simple command-line application using Ruby. I went through a number of steps before realising that the best way was to use RubyGems.

I started by creating my own command-line structure and then found this great article by [Todd Werth](http://blog.infinitered.com/entries/show/5).  He has a single Ruby file that you just replace various sections and viola you have a very simple command-line app.  But it wasn't quite what I wanted.  I didn't want to have to type:

    ./my_super_app 'some params'

I wanted to type:

    my_super_app 'some params'

I also wanted the structure of the application to follow a standard structure and I didn't want to put '.' into my path, for security reasons.
[Dr Nic](http://drnicwilliams.com/) came to the rescue with a couple of excellent articles describing how to [create gems using **newgem**](http://drnicwilliams.com/2006/10/11/generating-new-gems/) and specifically [command-line apps as gems](http://drnicwilliams.com/2006/10/18/create-and-deploy-command-line-apps-with-rubygems/).


##Installing newgem on Windows

To install **newgem** on Windows use the RubyGems install method.

    gem install newgem
    
This will install the gem and its dependencies.  However one of its dependencies is the [Hoe gem](http://seattlerb.rubyforge.org/hoe/), and there is a slight issue using it on Windows.


###What is hoe?

Hoe is a compliment to **rake**, it was created by [Ryan Davis](http://zenspider.com/) and provides **rake** tasks for testing, packaging, and releasing RubyGems.  [Geoffrey Grosenbach has a nice tutorial for using hoe](http://nubyonrails.com/articles/tutorial-publishing-rubygems-with-hoe) and also describes how to create a project structure using the **sow** task.

Hoe uses a **.hoerc** file for configuration, and this file should reside in the user's home directory, something like:

    C:\Documents and Settings\MyUserName

An example **.hoerc** file should look like the one below.  Please note that this is a [YAML](http://en.wikipedia.org/wiki/YAML) file and if you copy it from here remember to follow the indentation convention:

    ---
    publish_on_announce: false
    exclude: !ruby/regexp/tmp$|CVS|\.svn|_darcs|\.git|log$|local/.*\.rb$|Makefile|.*\.o$|.*\.bundle$|.*\.so$|.*\.dll$/ 
    str: ""
    "@taguri": tag:ruby.yaml.org,2002:regexp/tmp$|CVS|\.svn|_darcs|\.git|log$|local/.*\.rb$|Makefile|.*\.o$|.*\.bundle$|.*\.so$|.*\.dll$/
    blogs:
    - user: user
      url: url
      extra_headers:
        mt_convert_breaks: markdown
      blog_id: blog_id
      password: password


On Unix systems there is a shortcut to the user's home directory which is '~' and on Windows this isn't a shortcut.  Windows however does have an environment variable **HOMEPATH** which will point to the user's home directory.  The problem is that the Hoe gem code uses '~' in its **hoe.rb** file and this causes problems for Windows users.

###Patching the hoe.rb file
OK, be brave, lets tweak the hoe.rb file so that it will work on the Windows platform.
The Hoe gem **hoe.rb** file can be found in the following directory (assuming you installed Ruby to the standard location):

    C:\ruby\lib\ruby\gems\1.8\gems\hoe-1.6.0\lib

To make it work for Windows PCs you will need to modify this file with the following changes.  

Locate the **define_tasks** method:

    def define_tasks # :nodoc:
      def with_config # :nodoc:
        rc = File.expand_path("~/.hoerc")
        exists = File.exist? rc
        config = exists ? YAML.load_file(rc) : {}
        yield(config, rc)
      end

And add a new method above it such as this, and modify the line **rc = File.expand_path("~/.hoerc")** as below:
  
    def get_home_dir
      home_file_path = "~/"
      home_file_path = "#{ENV['HOMEPATH']}\\" if WINDOZE
      home_file_path
    end

    def define_tasks # :nodoc:
     def with_config # :nodoc:
       rc = File.expand_path(get_home_dir + '.hoerc')
       exists = File.exist? rc
       config = exists ? YAML.load_file(rc) : {}
       yield(config, rc)
     end

Finally you will need to locate and change the **task :config_hoe** so that it reads:

    "signing_key_file" => "#{get_home_dir}.gem/gem-private_key.pem",
    "signing_cert_file" => "#{get_home_dir}.gem/gem-public_cert.pem",

If all has gone correctly save this file and you are now ready to create a RubyGem.

##Creating a basic RubyGem

Using the **newgem** RubyGem enter the following command at the command prompt:

    newgem my_app -b greetings -T rspec -W

This will create a **my_app** subdirectory, with a command-line command app called *greetings* located in the **bin** directory.  It will use the testing framework *RSpec* and finally will not generate the RubyForge web site code.  This is assuming you don't want to relese your code as OpenSource.
The output will be something like this:

         create
         create  config
         create  doc
         create  lib
         create  script
         ... blah ...
         create  script/console
         create  script/console.cmd
         create  Manifest.txt
         readme  readme
    Important
    =========
    
    * Open config/hoe.rb
    * Update missing details (gem description, dependent gems, etc.)

If you want to add [RSpec Stories](http://blog.emson.co.uk/2008/06/understanding-rspec-stories-a-tutorial/) to this project there is a useful command-line generator to do this.  First navigate into the root of you new project app directory, in this case **my_app**.  Now execute the generate script:

    ruby script/generate install_rspec_stories

You should then get the following output:

      create  stories/steps
      create  stories/steps/my_app_steps.rb
      create  stories/sell_my_app.story
      create  stories/all.rb

Modify your **greetings** command so that it does something.  Do this by editing the file located here:

    my_app/bin/greetings

Open up this file and at the very end, add some code such as this, and save the file:

    # do stuff
    puts 'Greetings the world is my oyster!'


###Packaging caveat

As mentioned in the begining of this article I couldn't use **rake** to *tar* this project up for deployment to RubyForge.
The reason is that **rake** makes a Unix platform dependent call to *tar* the package using a Ruby task file **packagetask.rb** located:

    C:\ruby\lib\ruby\gems\1.8\gems\rake-0.8.1\lib\rake 
 
This means that on Windows you will have to manually *tar* this file.

However you can still create the gem using the rake task, and a gem file **my_app-0.0.1.gem**, will appear in the **pkg** directory:

    rake gem

This new RubyGem can now be installed by using the **gem install** commands:

    gem install pkg/my_app-0.0.1.gem

You should get the following output:

    For more information on my_app, see http://my_app.rubyforge.org
    
    NOTE: Change this information in PostInstall.txt
    You can also delete it if you don't want it.
    
     
    Successfully installed my_app, version 0.0.1
    Installing ri documentation for my_app-0.0.1...
    Installing RDoc documentation for my_app-0.0.1...


Finally you should now be able to execute your command-line command in the command prompt, simply by typing:

    greetings

I hope this is useful and helps with your understanding of how RubyGems work, specifically on Windows, also please jot down some comments if you have any ideas or suggestions.

##Links 
Here is a list of useful RubyGems links:

 *  [RubyGems.org](http://www.rubygems.org/)
 *  [Linux Journal article about RubyGems](http://www.linuxjournal.com/article/8967)
 *  [Dr Nic's generating new gems article](http://drnicwilliams.com/2006/10/11/generating-new-gems/)
 *  [Dr Nic's article for creating and deploying command line apps](http://drnicwilliams.com/2006/10/18/create-and-deploy-command-line-apps-with-rubygems/)
 *  [NubyOnRails article for using Hoe](http://nubyonrails.com/articles/tutorial-publishing-rubygems-with-hoe)
 *  [Jay Fields Ruby Project Tree](http://blog.jayfields.com/2006/10/ruby-project-tree.html)
