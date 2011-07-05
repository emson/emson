--- 
layout: post
title: Cannot select text with mouse in Microsoft Word, fix.
wordpress_id: 18
wordpress_url: http://blog.emson.co.uk/2008/08/cannot-select-text-with-mouse-in-microsoft-word-fix/
---
A friend had the following problem with their laptop the other day.
When they open Microsoft Word they cannot select any text with the mouse, they couldn't even place the cursor into the text by clicking the mouse on the text.

She said that the situation seemed to have occured when Norton Antivirus requested an update at the same time she was doing something in Word.

I suspected that it was that Word had somehow got corrupted, so I ran a repair, by inserting her Microsoft Office DVD and bring up the installer, and selecting *Repair*.  

This made no difference, I then reinstalled Office, still the problem persisted.  This made me think that the problem was related to the Registry, so I did a Google search.

##Fixing Word text selection

I found these articles: 

 *  [http://support.microsoft.com/kb/921541](http://support.microsoft.com/kb/921541) which describes how to troubleshoot problems with Microsoft Word 2007/2003/2002. (Really I'm not impressed it just stinks of shoddy programming.)
 *  [http://www.vistax64.com/vista-general/39390-cant-select-text-word-2007-vista.html](http://www.vistax64.com/vista-general/39390-cant-select-text-word-2007-vista.html)

To summarise the fix:

 *  Backup the Word\Data part of the Registry first
 *  delete the the Word\Data section
 *  restart Word 
 *  if it works then its completed, else restore the backup and keep Google searching.

###Warning about Word registry changes
This fix involves removing the Word/Data registry key.  By deleting this key you will loose some options you have set, this just means that you will need to reset them again.  Additionally you will loose the list of files in your *recently used files list* at the bottom of the file menu.  

Additionally be really careful about deleting anything out of the registry, because it can cause serious problems.  All the main Windows programs use it, and you could end up reinstalling everything including your copy of Windows.

###Backup the offending part of your computer's registry:

 1.  Exit all Office programs, Word, Excel, Outlook etc if they are running.
 2.  Open regedit by: Clicking **Start/Run**, then type **regedit**, and select **OK**.
 3.  Navigate the registry and select the appropriate Word registry subkey: 

     *  **Word 2002:** <br/>HKEY_CURRENT_USER\Software\Microsoft Office\10.0\Word\Data
     *  **Word 2003:** <br/>HKEY_CURRENT_USER\Software\Microsoft Office\11.0\Word\Data
     *  **Word 2007:** <br/>HKEY_CURRENT_USER\Software\Microsoft\Office\12.0\Word\Data

 4.  Select the Data node, then from the **File** menu choose **Export**.
 5.  Name this backup file something like **word_data.reg**, and save it your desktop.

###Delete the Word\Data registry node
 
 6.  Select the appropriate **Word\Data** node mentioned in part 3.
 7.  From the **Edit** menu select **Delete**, and then click **Yes**.
 8.  Exit regedit.
 
###Restart Word
 
 9.  Restart Word.  This will cause Word to recreate this registry node, but you may have to re-enter some of your Word options.  If everything is working as expected you can finish.  If you still have a problem follow the next section.


###Restore the backed up registry node

If the problem still persists then restore your registry back, and keep looking for a solution.
To restore the registry back to normal simply:

 *  Double-click the **word_data.reg** file on your desktop.
 *  and select **Yes**, and then **OK**.
