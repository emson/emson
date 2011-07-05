--- 
layout: post
title: "JavaScript The Good Parts Best Practices "
wordpress_id: 88
wordpress_url: http://blog.emson.co.uk/?p=88
---
##Part I

The best practice suggestions listed here are summaries from [Douglas Crockford's](http://www.crockford.com/) book: [JavaScript: The Good Parts](http://www.amazon.com/gp/product/0596517742?ie=UTF8&tag=emson-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0596517742)

By following these principles you will write better JavaScript code.

##JavaScript Best Practices

###1. Commenting

Avoid using `/* */` for commenting out your code instead use the `//` method.  The reason being that `*/` can occur in regular expressions and if you were to comment out a regular expression containing this, you would get a syntax error.


###2. Object Value Retrieval

For an object such as `var myObj = {key:"value"};` An object value can retrieved in two ways:
{% highlight javascript %}
                              
    myObj["key"];   // "value"
    myObj.key;      // "value"
    
{% endhighlight %}

However the second version using '.' notation is preferred as it reads better.


###3. Avoid Global Variables

Global variables will reduce the resiliency of your JavaScript programs so try to keep these to a minimum.  A best practice strategy for this is to use a single global variable for your program and simply assign objects to it. e.g.
{% highlight javascript %}
    var MYAPP = {};
    
    MYAPP.myObj = {
      key:"value"
    };
{% endhighlight %}

###4. Capitalize Constructor Functions

Functions that are created as *constructor functions* should be capitalized. Think of a *constructor functions* as a factory creating cloned functions.  Strictly this is not classical inheritance but **prototypical inheritance**. This is essentially where a function is cloned and contains all the properties of it's parent.
{% highlight javascript %}

    var Shape = function (name) {
      this.name = name;
    };
    
    var square = new Shape("square");
{% endhighlight %}

This style of constructor functions is not recommended as if you forget to use `new` then the `this` object gets bound to the global variable.


###5. Avoid Recursion

JavaScript does not offer *tail recursion optimization*, therefore functions that recurse too deeply will eventually fail.


###6. Use Function Scope To Protect Against Namespace Collisions

JavaScript does not have block scope, i.e. variables declared in a block statement (e.g. with in curly braces) are accessible from outside of that block.  However functions do have function scope, variables declared within a function cannot be accessed from outside of that function. 

Best practice is to declare the variables used in a function at the top of the function.

###7. Use Functions To Create Modules

A Module is a function or object that presents an interface but hides its state and implementation. This therefore avoids the use of global variables.
{% highlight javascript %}

    var myModule = function() {
      var call = '';
      
      return {
        setCall: function(myCall) {
          call = String(myCall);
        },
        doCall: function() {
          return 'Hello ' + call;
        }
      };
    }();  // the () will immediately invoke this function
    
    var myCaller = myModule();
    myCaller.setCall = 'Ben';
    alert( myCaller.doCall() );
    
{% endhighlight %}    
    
###8. Use Cascades

If you have a method that changes state, be sure to return `this` as opposed to `undefined`. The reason is that you can then chain these methods together.  This is how JQuery works.
{% highlight javascript %}

    getElement('myDiv').changeColour('red').setFont('Times').setStyle('italic');
    
{% endhighlight %}

###9. For Constructor That Take Many Parameters Use Object Specifiers

If an object takes many parameters it can be tricky ensuring that these parameters are in the correct order. Therefore create a single object instead that holds all the parameters as properties:
{% highlight javascript %}

    // Tricky
    var myObj = someObject(a, b, c, d);
    
    // Better
    var myObj = someObject({ first: a, second: b, third: c, fourth: d});
    
{% endhighlight %}

##General JavaScript Notes

Strings are immutable, in that once a string has been created that string object cannot be changed. However note that you can easily append and modify a string, the outcome just generates a new string. 

JavaScript provides a prototype linkage function, which when used allows one object to 'inherit' the properties of the linked object.

A `try catch` statement only allows a single catch block, to catch all exceptions. If you need to differentiate between exceptions then add code to inspect the name of the exception and handle it appropriately.


###Objects

Object creation methods:
{% highlight javascript %}

    var myObj = {};

{% endhighlight %}

Set a default value if an object property doesn't exist:
{% highlight javascript %}

    var myObj = {one:"1"};
    myObj.two || "2" // returns "2"
    
{% endhighlight %}

Assigning a value to an object property that is undefined will simply augment (or create) that property in that object and assign it.

Objects are passed by reference, they are never copied.

Every object is linked to a prototype object from which it can inherit properties. This object is `Object.prototype`

The prototype link is only used in the retrieval of properties from objects, if the first object does not have the property then JavaScript will use this prototype link to get the next object up the chain and try and retrieve the property from this object. If no property exists then `undefined` is returned.

When using `for in` for enumerating over properties in an object, be aware that it will also enumerate the properties of objects further up the prototype chain.  If you don't want this simply just use `for`,


###Functions

As Douglas Crockford says:

> Functions are the fundamental modular unit of JavaScript. Use functions for code reuse, information hiding and composition. Functions are used to specify the behaviour of objects.

Functions are objects, but they have a prototype link to `Function.prototype`, and then this root function object in turn links to `Object.prototype`.

A function can take 0 or more specified parameters, however functions get two extra hidden parameters `this` and `arguments`.

A **method** is simply a function assigned (bound) to an objects property. e.g.
{% highlight javascript %}

    var myObj = {
      myMethod: function (name) {
        alert("Hello " + name);
      };
    };
    
{% endhighlight %}    

A function always returns a value. If no return value is defined then the function will return `undefined`. However if a function is constructed with the `new` prefix and the return value is not an object the new object is returned.



##JavaScript Gotcha's

###typeof Returns incorrect types

Using the `typeof` method will return six results:

1. number
2. string
3. boolean
4. undefined
5. function
6. object

The problem occurs with `typeof ['1']` or `typeof null` which both return *object* which is incorrect.

###Function Invocation Binds 'this' To The Global Object

When a function is created by assigning it to a global variable, and the contents of that function contains a `this` statement then the `this` is bound to the Global Object

When a function, contains an internal function the `this` of the internal function is bound to the Global Object and not the `this` of the parent function. To fix this simply assign `this` to a `that` variable and use `that` within your internal function.
{% highlight javascript %}

    var myObj = {
      value: 0;
      
      double: function() {
        var that = this;
        var helper = function() {
          that.value = that.value + that.value;
        };
        helper();
      };
    };
    
{% endhighlight %}

###arguments Is An Array Like Object Not An Array

All functions have a parameter `arguments` which gives a function access to all the arguments supplied with it's invocation - including excess arguments that are not assigned to a parameter.
However note that `arguments` is in fact a special object with a length method and not an Array object. So `arguments` will lack methods that an Array object will have.





