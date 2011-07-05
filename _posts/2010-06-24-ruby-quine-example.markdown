--- 
layout: post
title: Ruby Quine Example
wordpress_id: 108
wordpress_url: http://blog.emson.co.uk/?p=108
---
I was recently challenged to write a **quine** in the language of my choice.
If you are unsure what a **quine** is you can find out from [http://en.wikipedia.org/wiki/Quine_(computing)](http://en.wikipedia.org/wiki/Quine_(computing\)).  However basically in our case it is a piece of code that when executed creates a copy of itself. This copy can then be executed and ad infinitum.

There are some official rules about how a **quine** should be implemented:

* A **quine** must not take any input, as this would allow the source code to be fed in from the keyboard.
* Following on from the previous rule a **quine**, should not open it's own file and display it's contents.



Anyway here is my solution in Ruby...
{% highlight ruby %}

    lambda{|x| puts x + x.inspect}.call "lambda{|x| puts x + x.inspect}.call"

{% endhighlight %}

Here is a brief description of what is happening. The lambda creates an anonymous function that takes one parameter and outputs it's value

Officially I think passing a value into the lambda `x` attribute violates one of the rules - but non-the-less it is quite a clear example and may be useful to get an idea of how a **quine** works.

###Links
Here is a list of interesting links that might help, good luck:

* An excellent overview of **quine's** with a Ruby slant: [http://blog.onideas.ws/ruby_quine](http://blog.onideas.ws/ruby_quine)
* Understanding the concepts of a **quine**: [http://research.swtch.com/2010/03/zip-files-all-way-down.html](http://research.swtch.com/2010/03/zip-files-all-way-down.html)
* Using a **quine** as an interview question: [http://www.skorks.com/2010/03/an-interview-question-that-prints-out-its-own-source-code-in-ruby/](http://www.skorks.com/2010/03/an-interview-question-that-prints-out-its-own-source-code-in-ruby/)
* Shortest Ruby **quine**: [http://stackoverflow.com/questions/2474861/shortest-ruby-quine](http://stackoverflow.com/questions/2474861/shortest-ruby-quine)
* More **quine** links: [http://www.nyx.net/~gthompso/quine.htm](http://www.nyx.net/~gthompso/quine.htm)

