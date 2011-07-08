--- 
layout: post
title: Installing the Latest RSpec TextMate Bundle
wordpress_id: 9
wordpress_url: http://blog.emson.co.uk/?p=9
---
#Installing the Latest RSpec TextMate Bundle

If you are using [TextMate](http://www.macromates.com) on your Apple Mac you may wish to download the RSpec TextMate Bundle and install it.  The TextMate bundle can be found in the [RSpec Git repository](http://github.com/dchelimsky/rspec-tmbundle/tree/master).  In your **Terminal.app** enter the following commands.  Please note that you will need Git installed on your Apple Mac in order to do this, please see my article [Installing Git on Apple OSX](http://blog.emson.co.uk/2008/06/installing-a-git-client-on-osx/) on how to do this:

    cd /Applications/TextMate.app/Contents/SharedSupport/Bundles
    git clone git://github.com/dchelimsky/rspec-tmbundle.git RSpec.tmbundle

You should get output like this in your **Terminal** window:
  
    Initialized empty Git repository in /Applications/TextMate.app/Contents/SharedSupport/Bundles/RSpec.tmbundle/.git/
    remote: Counting objects: 46199, done.
    remote: Compressing objects: 100% (10514/10514), done.
    Indexing 46199 objects...
    remote: Total 46199 (delta 33049), reused 46199 (delta 33049)
     100% (46199/46199) done
    Resolving 33049 deltas...
     100% (33049/33049) done
  
Now you will need to reload your TextMate Bundle Editor for this new bundle to be applied.  In TextMate select the following menu items and button:

      Bundles > Bundle Editor > Reload Bundles

If you ever need to get a more uptodate version of the **RSpec.tmbundle** then simply navigate to your TextMate Bundle directory and use Git to pull the latest version down to your machine.  Don't forget to reload your bundles once this has been done.  To pull the latest version from the Git hub use this command:

    git pull

For older instructions on how to do this see [Robby on Rails - RSpec Bundle for TextMate](http://www.robbyonrails.com/articles/2007/02/12/rspec-bundle-for-textmate)

