--- 
layout: post
title: How to Automatically Log In Using SSH On Dreamhost
wordpress_id: 74
wordpress_url: http://blog.emson.co.uk/?p=74
---
#How to Automatically Log In Using SSH On Dreamhost

If you build web sites and [host (or want to host) them on Dreamhost](http://www.dreamhost.com/r.cgi?105422), you will be well aware that at some stage you will be using SSH to log in and out of your hosting server. If you have many sites then this gradually becomes tiresome - especially if you use tools like [Capistrano](http://www.capify.org/index.php/Capistrano) to deploy your applications, as you will constantly have to re-enter your passwords. 

Fortunately you can certify your local computer, such that it will automatically enter the SSH password for you. Here are some quick tips on how to do it.


###Generate Your Security Keys

Generate your private/public key pair on your local machine, from your home directory:

    ssh-keygen -d
    
    Now DON'T enter a password - just press return, otherwise you 
    will still need to enter a password, when you log in with SSH.
    
###Set Privileges And Copy The Public Key To Your Server
    
Use the following command to copy your newly created **~/.ssh/id_dsa.pub** file up to the server you wish to remote log in to.  Note that this command below will set the permissions both locally and remotely. The remote .ssh/* needs to be set to 0600.

N.B. Replace USERNAME and MY_DOMAIN with your own username and domain.

    ssh USERNAME@ftp.MY_DOMAIN.COM 'test -d .ssh || mkdir -m 0700 .ssh ; cat >> .ssh/authorized_keys && chmod 0600 .ssh/*' < ~/.ssh/id_dsa.pub

      Enter the normal USERNAME password.

###Log In Using SSH 

Now log in using:

    ssh USERNAME@ftp.MY_DOMAIN.COM
    
    You shouldn't neet to enter a password.

###Taking It Further

To simplify things even further you can modify the **~/.ssh/config** file such:

    Host NICKNAME
    Hostname MY_DOMAIN.COM
    User USERNAME
    
Now you only need to type:

    ssh NICKNAME
    
    And you will be automatically logged in.
    
