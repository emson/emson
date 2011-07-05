--- 
layout: post
title: What is the Difference Between a Proc and a Lambda in Ruby?
wordpress_id: 104
wordpress_url: http://blog.emson.co.uk/?p=104
---
The concepts of a **Proc** and a **lambda** in Ruby are subtly different and this post aims to try and explain the differences.

##Procs and Lambdas
Firstly why do we need Procs and lambdas? In order to answer this you need to understand what a **block** is in Ruby.

###Understanding what a block is?
A **block** is simply a piece of code, which is usually attached to a method, Proc or lambda. It is important to note that this block of code is NOT an object (yet...), it is code that is defined either within braces {} or within a `do end` container.

The code in a **block** is just logic that hasn't been wrapped into an object yet. When this **block** is passed to a method and that method has a **yield** statement then Ruby will wrap that **block** into a special type of object. That type of object is a **Proc** object. Once the **block** becomes a **Proc** Ruby can use it and the **block** code 'comes alive'.

Therefore we need a **Proc** object in order to handle **blocks**. The next question is how does a **lambda** fit in.

A **lambda** is a type of **Proc** object, it is almost completely identical to a Proc apart from two differences. This little code snippet shows that a **Proc** and a **lambda** are derived from the same superclass, **Proc**.
{% highlight ruby %}

      def what_are_lambdas_and_procs
        puts "--- #{__method__} ---"
        lam = lambda { puts 'lam variable assigned a lambda' }
        lam.call
        puts "lam is a class of: #{lam.class.name}"
        puts "lam method count: #{lam.methods.size}"
        puts
        prc = Proc.new { puts 'prc variable assigned a Proc' }
        prc.call
        puts "prc is a class of: #{prc.class.name}"
        puts "prc method count: #{prc.methods.size}"
        ""
      end
{% endhighlight %}

This will output:
{% highlight ruby %}

      --- what_are_lambdas_and_procs ---
      lam variable assigned a lambda
      lam is a class of: Proc
      lam method count: 55

      prc variable assigned a Proc
      prc is a class of: Proc
      prc method count: 55

{% endhighlight %}

##So what are the subtle differences between Proc and lambda?

As mentioned there are two differences, and these differences are :

1. in how a Proc/lambda assigns arguments, and
2. in what happens, when a Proc/lambda calls return within a caller method.

###Proc, lambda argument assignment

When a **lambda** is called with more or less than the required number of arguments Ruby will throw an ArgumentError.

However when a **Proc** is called with more arguments, no error is thrown and the extra values are simply thrown away.  When it is called with less arguments these parameters are simply assigned a value of **nil**.

These two code snippets demonstrate this:
{% highlight ruby %}

      def lambda_args
        puts "--- #{__method__} ---"
        lam = lambda { |a, b| puts "lambda with 2 arguments a:#{a}, b:#{b}" }
        lam.call( "first", "second")
        puts "lambda called with three arguments..."
        begin
          lam.call("first", "second", "third")
        rescue
          puts "   ArgumentError: wrong number of arguments (3 for 2)"
        end
        puts "lambda called with no arguments..."
        begin
          lam.call()
        rescue
          puts "   ArgumentError: wrong number of arguments (0 for 2)"
        end
      end

      def proc_args
        puts "--- #{__method__} ---"
        prc = Proc.new { |a, b| puts "Proc with 2 arguments a:#{(a.nil?) ? "nil" : a }, b:#{(b.nil?) ? "nil" : b }" }
        prc.call( "first", "second")
        puts "Proc called with three arguments..."
        prc.call("first", "second", "third")
        puts "Proc called with no arguments..."
        prc.call()
      end
{% endhighlight %}
This will output:
{% highlight ruby %}

      --- lambda_args ---
      lambda with 2 arguments a:first, b:second
      lambda called with three arguments...
         ArgumentError: wrong number of arguments (3 for 2)
      lambda called with no arguments...
         ArgumentError: wrong number of arguments (0 for 2)

      --- proc_args ---
      Proc with 2 arguments a:first, b:second
      Proc called with three arguments...
      Proc with 2 arguments a:first, b:second
      Proc called with no arguments...
      Proc with 2 arguments a:nil, b:nil

{% endhighlight %}

###Proc and lambda return behaviour

When a **lambda** returns a value and this **lambda** is called within a method then method will simply continue it's execution until it itself returns a value.

However when a **Proc** returns a value and this **Proc** is called within a method this containing method will be exited WITHOUT any further execution. This is slightly surprising behaviour, and consequently a **Proc** should be handled with care.

These two code snippets demonstrate this behaviour:
{% highlight ruby %}

      def lam_return
        puts "--- #{__method__} ---"
        puts "simple function that calls a lambda and then returns it's own string."
        my_lam = lambda { return 'Lambda has returned' }
        my_lam.call 
        return 'lam_return method string returned'
      end

      def proc_return
        puts "--- #{__method__} ---"
        puts "simple function that calls a Proc, \nBUT instead of returning it's own string returns the Proc's string."
        my_proc = Proc.new { return 'Proc string returned' }
        my_proc.call # this function returns and finishes here
        return 'proc_return method string returned' # the return is never called
      end

{% endhighlight %}

This will output the following:
{% highlight ruby %}

      --- lam_return ---
      simple function that calls a lambda and then returns it's own string.
      lam_return method string returned

      --- proc_return ---
      simple function that calls a Proc, 
      BUT instead of returning it's own string returns the Proc's string.
      Proc string returned

{% endhighlight %}

####Proc return work around
There is however a very simple work around to avoid a Proc from returning out of it's caller method. Simply removed the return statement, because Ruby always returns the last line this last line will be returned automatically.
{% highlight ruby %}

      def proc_return_work_around
        puts "--- #{__method__} ---"
        puts "simple function that calls a Proc, \nhowever the method string still returns."
        my_proc = Proc.new { 'Proc string returned' }
        my_proc.call # this function returns and finishes here
        return "#{__method__} method string returned" # the return is never called
      end

{% endhighlight %}

This will output:
{% highlight ruby %}

      --- proc_return_work_around ---
      simple function that calls a Proc, 
      however the method string still returns.
      proc_return_work_around method string returned

{% endhighlight %}

##Conclusion

As you have seen there are only two small differences between a **Proc** and a **lambda** but these differences have quite surprising behaviours.  Therefore following the Ruby principle of *"...least surprise"*, it is best practice to use **lambdas** over **Procs** unless you absolutely need the **Proc** behaviour.


For more information please see this article: [http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Method_Calls](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Method_Calls)


