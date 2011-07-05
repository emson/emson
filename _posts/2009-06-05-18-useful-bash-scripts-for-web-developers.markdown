--- 
layout: post
title: 18 Useful bash scripts for web developers
wordpress_id: 58
wordpress_url: http://blog.emson.co.uk/?p=58
---
##Using bash scripts to become a more efficient web developer

Here are a few scripts, that I find really useful for speeding up my web development time.  

I've been building up this list as I needed to use them - so they maybe a little raw.

For example often clients send me images with filenames that don't match my naming standard, so running the appropriate script really helps keep me focussed on the job in hand and not waste too much time reformatting filenames etc.

Finally if you have any useful little bash scripts why don't you add them to the comments below?


###Bash script to append a .txt extension to a filename

Iterate over the current directory, get all files with .log and append .txt to the end of the entire filename:

    for i in *.log*; do mv "$i" "$i.txt"; done


###Script to make filenames lowercase

Converts all the file names in a directory and converts them to lowercase.

echo version:

    for i in *.log*; do echo mv \"$i\" \"`echo $i| tr [A-Z] [a-z]`\"; done

real version:

    for i in *.txt; do mv "$i" "`echo $i| tr [A-Z] [a-z]`"; done


###Quick note on how to use grep to search for a string in file

-o print Only the matched parts of a matching line, with each part on a separate line.
-H print the filename for each match.
-n prefix each line of output with the 1 based line number.
-R, -r recursively read all files under subdirectories.

    grep -oHnr "some pattern"  *.txt

{txt,log} this globs the file extensions together.
2>/dev/null this passes all errors to a blackhole, so that they won't be displayed.

    grep 123741 ./adapter_logs/*.{txt,log} 2>/dev/null

Find all occurrences of 123741 in all .txt and .log files in all subdirectories.

    for i in 'find . -type d'; do grep 123741 $i/*.{txt,log}; done 2>/dev/null


###List all files that match this regular expression in the current direcory

All files with the number 5 in their name.

    ls | grep -e .*5.*


###Bash script to check whether a tag exists in a subversion repository

    svn ls http://www.mysvnserver.co.uk/myproject/tags | grep mytag-0.7.0.1/

The [ ] is known as a Test, by default it tests integers
Also to end an if statement you use fi

    if [ 1 = 1 ] ; then echo YES; else echo NO; fi

    if [ "`svn ls http://mysvnserver/mysvnrepository/myproject/tags | grep R-9.20.400.0/`" = "R-9.20.400.0/" ]; then echo YES; else echo NO; fi

Note need the / in the evaluation part of "it-0.9.0.7/", don't really need it in grep it-0.9.0.7/

    $ if [ "`svn ls http://www.mysvnserver.co.uk/myproject/tags | grep it-0.7.0.1/`" = "it-0.7.0.1/" ]; then echo YES; else echo NO; fi



###Quick note on how to increment a variable, in a bash script

    j=0; j=$(($j+1))

incrementing in a loop

    j=0;for i in *.txt; do echo "some_file_"$j".txt"; j=$(($j+1)); done


###Using a bash for loop to list all text files and append whoopee

    for i in *.txt; do echo $i"whoopee"; done


###Change all filenames to be a specific incremented name, starting from file 16

    j=16;for i in *.jpg; do mv "$i" "gallery_"$j".jpg"; j=$(($j+1)); done;ls


###Script to loop through all .txt filenames and make them lower case

    for i in *.txt; do mv "$i" "`echo $i| tr [A-Z] [a-z]`"; done


###Get all JPG files and create the appropriate HTML list tags for them and add them to a file

Create an HTML version:

    for i in *.jpg; do echo "\t<li>\r\t\t<img src='images/$i' alt='' />\r\t</li>"; done > list_items.html

Create a HAML version:

    for i in *.jpg; do echo "\t%li\r\t\t%img{ :src => 'images/$i', :alt => '' }\r"; done > imgs.haml


###Batch change a misspelled filename of certain files, using string replacement

    for i in aples*.jpg; do mv $i ${i/aples/apples} ; done
    

###Script to batch create files based on filenames in a file

Create a file called something like "my_files.txt" with the following content:

> archive_error_112480_0040.txt
> archive_error_114390_0043.txt
> etc...

Commands: 

    for i in `sed -n -e 'p' my_files.txt`; do mkdir a_test/$i; done
    or
    while read x; do touch $x; done < my_files.txt
    or
    cat my_files.txt | xargs touch
    (xargs passes each line as an argument into EACH touch)



###Using bash Expansions, and allow escapes

-e is an undocumented flag that allows escapes
Expansion ${command/parameter/substitution} everything else needs to be escaped.

    echo -e ${PATH//\:/\\n}


###Executing a .bash_script after making changes

    source .bash_profile
    or
    . .bash_profile

NB: you can 'include' other bash .sh scripts in a .bash_profile file by using 'source' or '.'


###Quick tip on using curl for downloading files off the web

Use -O to name the remote file, curl will save it locally but remove the rest of the path.

    curl -O http://rubyforge-files.ruby-forum.com/rubygems/rubygems-0.9.0.tgz


###Bash command for testing the existence of files and directories

If a directory does NOT exist create it

    [ -d "/var/cache/git" ] || mkdir /var/cache/git

If a file exists echo a message:

    [ -f "./new_file.txt" ] && echo "its there"


###Bash command to remove spaces from filenames

    for i in *.html; do mv "$i" "`echo $i| tr -d ' '`"; done

replace spaces for underscores

    for i in *.html; do mv "$i" "`echo $i| tr ' ' '_'`"; done


Vim regex to replace spaces in images file names of ah HTML page

    :%s:\(images\/\)\(\w\+\)\s\(\w\+\.jpg\):\1\2_\3:g


###Copy text to the clipboard

    echo 'hello world'|pbcopy
    cmd + v


##Conclusion

Some of these are quite basic but useful none-the-less, please add your own to the comments below.
Many thanks, and good luck.

##Updated...

###Script function for .bash_profile to cd to the last opened finder location

This is a script written by a colleague.  [James Power](http://www.visualcortex.co.uk/).
Add the following function to your **.bash_profile** in Apple OSX.  Now open a directory in Finder, then open terminal and type **cdf**, your terminal will now change directory to the same one as the last Finder opened.


    # function to change directory to the one set in the last opened finder.
    cdf () {
       currFolderPath=$( /usr/bin/osascript <<"			EOT"
           tell application "Finder"
               try
                   set currFolder to (folder of the front window as alias)
               on error
                   set currFolder to (path to desktop folder as alias)
               end try
               POSIX path of currFolder
           end tell
    			EOT
       )
       echo "cd to \"$currFolderPath\""
       cd "$currFolderPath"
    }


Also for a much more comprehensive list of commands check out: [http://www.commandlinefu.com/commands/tagged/34/bash](http://www.commandlinefu.com/commands/tagged/34/bash)

###Ruby Rush command-line tool
If you use **Ruby** maybe check out [Rush](http://rush.heroku.com/), it allows you do use Ruby in a bash command-line like way.



