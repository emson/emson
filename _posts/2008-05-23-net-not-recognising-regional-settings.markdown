--- 
layout: post
title: .Net not recognising regional settings
wordpress_id: 3
wordpress_url: http://blog.emson.co.uk/?p=3
---
#.Net not recognising regional settings
The other day I had the following problem.
One of my test servers had been setup with US regional settings and should have been setup with UK settings.

The problem had been unnoticed for a while until our C# .Net application tried to call a stored procedure on a database on another server. This database server had UK regional settings. 
The stored procedure expected to get a date in UK 'Long format'.  The error message was a database error saying something like:

	convertion error can't convert from NVarChar to DateTime

As the application was already compiled and being used on a live server I could not modify the code, I therefore had to run SQL Profiler and see what values were being passed into the stored procedure.  This is where I saw the US 'Long Date' format being passed in.

<!--more-->

##.Net regional settings fix##

So I tried changing the regional settings on my server back to the UK, but the application was still sending the US 'Long Date' value.
I dug real deep and suspected that IIS was doing something funny and found further regional settings in an **Advanced** tab, but still this didn't fix the issue.

The problem I soon discovered is that when you install the .Net framework it internalises the regional settings but when you try and change them on the server these internalised settings don't get changed.  
The only solution to this is then to modify the <b>machine.config</b> file.

The <b>machine.config</b> file is in each installation of the .Net framework.  I happen to have both .Net 1.1 and .Net 2.0, however I changed the <b>machine.config</b> file in only the .Net 1.1 installation directory (even though our application is .Net 2.0) and all worked fine.
The <b>machine.config</b> can be found in this directory:
	<pre>C:\WINDOWS\Microsoft.NET\Framework\v1.1.4322\CONFIG</pre>

If you are using .Net 2.0 I'm sure you can make the same changes to the **globalization** XML node.
Please change the **globalization** XML node to have the correct cultural settings in our case **en-GB**:
<code><pre>
	<globalization
		fileEncoding="utf-8"
		requestEncoding="utf-8"
		responseEncoding="utf-8"
		culture="en-GB"
		uiCulture="en-GB"							
	/>
</pre></code>

To test that a .Net application would send the correct settings I wrote a simple test app which would print the 'Long Date' to the console.  Here is the source code if you want to quickly test it yourself:

**ShowLongDate.cs**

{% highlight c# %}
using System;
	
namespace examples
{
    class ShowLongDate
    {
        static void Main(string[] args)
        {
            DateTime current = DateTime.Now;
            string longDate = current.ToLongDateString();
            System.Console.WriteLine(string.Format("Date as longDate: {0}", longDate));
        }
    }
}
{% endhighlight %}

##Interesting things I learned##

You can open the regional settings from the commandline or from **Start/Run** by typing

        intl.cpl

Also all the regional settings are located in the registry here:

        HKEY_CURRENT_USER/Control Panel/International

Useful URLs:
<a href="http://www.microsoft.com/globaldev/reference/localetable.mspx">http://www.microsoft.com/globaldev/reference/localetable.mspx</a>

Anyway I hope this helps if you have the same problem.




