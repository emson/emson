--- 
layout: post
title: Installing Git on Apple OSX
wordpress_id: 8
wordpress_url: http://blog.emson.co.uk/2008/06/installing-a-git-client-on-osx/
---
#Installing Git on Apple OSX

Git is a version control system like CVS and Subversion.  It was created by Linus Torvalds and used for Linux.  It has recently been adopted by the Ruby community and many of the new Ruby and Ruby on Rails projects now use it.  One of the reason's for it's popularity amongst the Ruby community is because of [github](http://github.com/).  In there own words:
> GitHub is the easiest (and prettiest) way to participate in that collaboration: 
> fork projects, send pull requests, monitor development, all with ease.

##How to quickly setup Git on Apple OSX##
The simplest way to install Git on Apple OSX (for Intel Mac) is to use this pre-packaged installer: [http://metastatic.org/text/Concern/2007/08/18/git-package-for-mac-os-x/](http://metastatic.org/text/Concern/2007/08/18/git-package-for-mac-os-x/), however if you have a PowerPC Mac or wish to use an alternative installation method, try the information on this site: [http://bc.tech.coop/blog/070827.html](http://bc.tech.coop/blog/070827.html)

Launch the installer, and accept the license agreements.  Once the installer has completed open up OSX Terminal.app (*cmd + space*, then type terminal), and type **git** at the command prompt, if all has been successful you should see something like this:

    usage: git [--version] [--exec-path[=GIT_EXEC_PATH]] [-p|--paginate|--no-pager] [--bare] [--git-dir=GIT_DIR] [--work-tree=GIT_WORK_TREE] [--help] COMMAND [ARGS]

    The most commonly used git commands are:
       add           Add file contents to the index
       apply         Apply a patch on a git index file and a working tree
       archive       Create an archive of files from a named tree
       bisect        Find the change that introduced a bug by binary search
       branch        List, create, or delete branches
       checkout      Checkout and switch to a branch

    ...  continued with more commands ...

###Configuring Git###
One of the interesting things about Git is that it can be configured from the command line.  Therefore the first thing to do is setup your user details so that when you add changes it uses your details.  This web site has some very good instructions on how to get up and running using Git very quickly [http://git.or.cz/course/svn.html](http://git.or.cz/course/svn.html).

To configure your Git user details enter the following at the command prompt:

    git config --global user.name "Your Name Comes Here"
    git config --global user.email you@yourdomain.example.com

###Clone / Checkout the RSpec Git Repository###
Now you should be able to clone (Subversion calls this *checkout*) a repository so that you can view the source code.  To do this enter the following command at the command prompt.  I intend to checkout the [RSpec](http://github.com/dchelimsky/rspec/tree/master) repository from github:

    git clone git://github.com/dchelimsky/rspec.git

All being well you should see output in your Terminal like this:

    Initialized empty Git repository in /Users/myname/my_repositories/rspec/.git/
    remote: Counting objects: 46810, done.
    Compressing objects: 100% (10646/10646), done.)   
    Indexing 46810 objects...
    remote: Total 46810 (delta 33521), reused 46810 (delta 33521)
     100% (46810/46810) done
    Resolving 33521 deltas...
     100% (33521/33521) done
    
Now you should have all the source code in your new repository.
