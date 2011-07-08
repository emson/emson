--- 
layout: post
title: Create Sinatra Partials The Rails Way
wordpress_id: 71
wordpress_url: http://blog.emson.co.uk/?p=71
---
#Create Sinatra Partials The Rails Way

This is just a quick post. I've been playing around with the [Sinatra micro web framework](http://www.sinatrarb.com/), and have to say that it is excellent. It really is the perfect tool for rapidly creating small and useful [Ruby](http://www.ruby-lang.org/en/) micro-sites.

Being small and lightweight it doesn't have many of the features of larger frameworks like [Ruby on Rails](http://rubyonrails.org/). One of the features I've begun to need is the handling of Rails like partials.

Notably I need to pass both local variables and collections to the partial code and have the partials rendered accordingly.
I started implementing my own version, and then came across a nice little [Github](http://github.com/) project: [Sinatra-Helpers by sbfaulkner](http://github.com/sbfaulkner/sinatra-helpers).

Anyway here is the code he uses for Sinatra partials:
{% highlight ruby %}
def haml_partial(name, options = {})
  item_name = name.to_sym
  counter_name = "#{name}_counter".to_sym
  if collection = options.delete(:collection)
    collection.enum_for(:each_with_index).collect do |item,index|
      haml_partial name, options.merge(:locals => {item_name => item, counter_name => index+1})
    end.join
  elsif object = options.delete(:object)
    haml_partial name, options.merge(:locals => {item_name => object, counter_name => nil})
  else
    haml "_#{name}".to_sym, options.merge(:layout => false)
  end
end
{% endhighlight%}

You can use his project or if you just need this feature just pop this code into your Sinatra helpers file  __application_helper.rb__:
{% highlight ruby %}
helpers do

  def partial(name, options = {})
    item_name = name.to_sym
    counter_name = "#{name}_counter".to_sym
    if collection = options.delete(:collection)
      collection.enum_for(:each_with_index).collect do |item, index|
        partial(name, options.merge(:locals => { item_name => item, counter_name => index + 1 }))
      end.join

    elsif object = options.delete(:object)
      partial name, options.merge(:locals => {item_name => object, counter_name => nil})
    else
      haml "_#{name}".to_sym, options.merge(:layout => false)
    end
  end
end
{% endhighlight%}

Now you can call the partial from a your page e.g. __index.haml__
{% highlight haml %}
= partial("mypartial", :locals => {:var1 => "value 1", :var2 => "value 2 and so on"})
{% endhighlight%}

Create a partial in the same directory as the calling page. Note that this code doesn't like filenames truncated with a '-', so avoid this if possible. Also like in Rails your partials should start with an underscore. e.g.  **_mypartial.haml**

To access the variable from this partial simply call the local variable name in your code:
{% highlight haml %}
%h2 
  This is var1:
  = var1
  
%p 
  And this is var2:
  = var2
{% endhighlight%}

Handling collections, again, is similar to the way Rails handles partial collections. From your page call the partial with a collection such as:
{% highlight haml %}
= partial("listitem", :collection => ["item 1", "item 2 and so on"])
{% endhighlight%}
    
Now in the **_listitem.haml** partial code simply provide the partial name as a variable or the partialname with _counter for the increment number e.g.
{% highlight haml %}
%li
  = listitem
  is the 
  = listitem_counter
  in this array
{% endhighlight%}
      
Anyway all pretty basic stuff but I hope it helps the casual Sinatra surfer.

Goodluck.
