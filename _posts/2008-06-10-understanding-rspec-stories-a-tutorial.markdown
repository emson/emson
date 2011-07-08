--- 
layout: post
title: Understanding RSpec Stories - a Tutorial
wordpress_id: 12
wordpress_url: http://blog.emson.co.uk/2008/06/understanding-rspec-stories-a-tutorial/
---
#Understanding RSpec Stories - a Tutorial

When I initially started writing this RSpec guide I had planned to cover both traditional RSpec *Specs* and the new *Stories* feature, however the guide quickly became to big so I decided to focus purely on RSpec Ruby Stories, as opposed to RSpec Rails Stories.

##Why Unit Testing?

Every pragmatic programmer tells us that we should be writing unit tests, but are they really that important?  Well unfortunately they are.  Unit test ensure that we write good quality code mitigating the number of bugs we introduce into a software system.
However more than that, they help us think about the problem from different perspectives which leads to further insights and gradually makes the domain problems more manageable.  

[Unit testing](http://en.wikipedia.org/wiki/Unit_test) is especially important when the language you use is interpreted.  With compiled languages like C and C++ for example, the compiler picks up on compile time errors, however with interpreted languages like Ruby, Perl and Python there is no compiler and bugs can easily be introduced which won't be uncovered until the interpreter executes that branch of the code.

Traditionally tests have been written using a Unit Test framework like JUnit, NUnit or RUnit.  However it is easy to spending a lot of time writing tests that test every unit of code in your software system, what RSPec does is to provide a subtle shift of focus from Unit testing to Behaviour testing or [Behaviour Driven Development (BDD)](http://behaviour-driven.org/).  By focusing on the behaviour of the system it helps clarify in our minds what the system should actually be doing.  It also means that our energy is directed at more 'useful' tests.  Useful tests, cover what the system should be doing and build in enough redundancy so that it should be easy to [refactor](http://en.wikipedia.org/wiki/Refactoring) our code without having to re-write every test.


##What is RSpec?  Give me some background.

RSpec is a Behavioural Driven Development (BDD) tool, originally created by Dave Astels and Steven Baker.

However [David Chelimsky](http://blog.davidchelimsky.net) is really the gatekeeper of the RSpec project, and because it is an OpenSource project you can find the code on the [GitHub RSpec Repository](http://github.com/dchelimsky/rspec/tree/master).  If you would like to use [Git](http://en.wikipedia.org/wiki/Git_%28software%29) to 'check out' a copy of the source code see my article:  [Installing Git on Apple OSX](http://blog.emson.co.uk/2008/06/installing-a-git-client-on-osx/)

RSpec is really two projects merged into one.  The RSpec project pages describes these merged projects as:

*  a Story Framework for describing behaviour at the application level
*  a Spec Framework for describing behaviour at the object level

[Dan North created rbehave](http://dannorth.net/2007/06/introducing-rbehave) which is the Story framework and he describes it as:

>  **rbehave** is a framework for defining and executing application requirements. 
>  Using the vocabulary of behaviour-driven development, you define a feature 
>  in terms of a Story with Scenarios that describe how the feature behaves. 
>  Using a minimum of syntax (a few â€œquotesâ€ mostly), this becomes an executable 
>  and self-describing requirements document.

The Spec framework was created by David Chelimsky, and is orientated at testing the behaviour of objects in your system.

By encompassing two frameworks RSpec equips a programmer with a thorough set of testing tools, allowing you to think about your software problem from a number of perspectives.

##What is an RSpec Story?

[Dan North](http://dannorth.net/whats-in-a-story) has a good article about what a Story actually is, but here is a quick summary.  A Story describes the functionality of a specific software feature, and it describes it in a way that is easy to understand from the point of view of a client.  In fact a story should be thought of as a conversation between a client and a programmer over some feature of the software. <br/>
As this story unfolds the programmer guides the client by prompting different **Scenarios** and using specific words **Given, When** and **Then**, to capture the essence of the systems behaviour.

A typical story template will take this structure but does not necessarily have to:

    Title (one line describing the story)

    Narrative:
    As a [role]
    I want [feature]
    So that [benefit]

    Acceptance Criteria: (presented as Scenarios)

    Scenario 1: Title
    Given [context]
      And [some more context]...
    When  [event]
    Then  [outcome]
      And [another outcome]...

    Scenario 2: ...

The key points to a story are:

*  **Title** - this should describe an activity or action
*  **Narrative** - should include a *role*, *feature* and a *benefit*. "As a [role] I want [feature] so that [benefit]"
*  **Scenario** - describes what is different in the story. NB you can have a number of different scenarios.
*  **Scenario: Given/Events/Outcomes** - Given [some context], When [I do something], Then [this happens].  It is important to use the Given, When, Then language.
*  **Givens** should define all of, and no more than, the required context
*  **Event** should describe the feature

RSpec actually has [four implementations of a Story](http://blog.davidchelimsky.net/articles/2007/10/21/story-runner-in-plain-english), which given the sporadic documentation can lead to much confusion.  Essentially from an RSpec point of view a Story consists of three parts:

*  the **Story**
*  **Steps** that the Story undertakes
*  **Components** to be tested, described by the Story

###Installing RSpec on Apple OSX

The quickest and easiest way to install RSpec is to use Ruby gems.  In the Apple **Terminal.app** command prompt enter the following command, and install RSpec as root:

    sudo gem install -r rspec

It should be noted that according to how you have setup your Mac, you may need to use **sudo** to install this gem.
If all goes well you should get output such as this:

    Bulk updating Gem source index for: http://gems.rubyforge.org/
    Successfully installed rspec-1.1.4
    1 gem installed
    Installing ri documentation for rspec-1.1.4...
    Installing RDoc documentation for rspec-1.1.4...

Finally if you are an Apple user and are using [TextMate](http://www.macromates.com) you might want to read my article on how to [install the TextMate RSpec bundle](http://blog.emson.co.uk/2008/06/installing-the-latest-rspec-textmate-bundle/).

##How to Create an RSpec Story

The simplest type of RSpec Story consists of a single file that contains both the Story and the executable Steps, all you need to do is require the library or class you wish to test.<br/>
For example if we wish to use RSpec to test an **Account** object you would create your library file such as **account.rb**:
{% highlight ruby %}
class Account
  attr_accessor :balance

  def initialize(amount)
    @balance = amount
  end

  def transfer_to(account, amount)
    @balance = @balance - amount.to_f
    account.balance = account.balance.to_f + amount.to_f
  end
end
{% endhighlight %}

Now create your RSpec Story and save this file as **account_story.rb** with the following content.  Note that on Apple OSX systems you seem to have to require the **rubygems** library:
{% highlight ruby %}
require 'rubygems'
require 'spec'
require 'spec/story'
require 'account'

Story "transfer to cash account",
%(As a savings account holder
  I want to transfer money from my savings account
  So that I can get cash easily from an ATM) do

  Scenario "savings account is in credit" do
    Given "my savings account balance is", 100 do |balance|
      @savings_account = Account.new(balance)
    end
    And "my cash account balance is", 10 do |balance|
      @cash_account = Account.new(balance)
    end
    When "I transfer", 20 do |amount|
      @savings_account.transfer_to(@cash_account, amount)
    end
    Then "my savings account balance should be", 80 do |expected_amount|
      @savings_account.balance.should == expected_amount
    end
    And "my cash account balance should be", 30 do |expected_amount|
      @cash_account.balance.should == expected_amount
    end
  end

  Scenario "savings account is overdrawn" do
    Given "my savings account balance is", -20
    And "my cash account balance is", 10
    When "I transfer", 20
    Then "my savings account balance should be", -40
    And "my cash account balance should be", 30
  end
end
{% endhighlight %}

Note that although you did not explicitly define the code in the Steps of the second Scenario the RSpec framework uses the **Given, When, Then** methods defined in the first Scenario.
In fact if you look at the Story and think of it as a plain old Ruby code file you gradually understand what is happening.  The file defines a number of methods **Story, Scenario, Given, When** and **Then**, note that when RSpec analyses this Story file it parses the two Scenarios and is able to match the **Given, When, Then** methods with the other Scenario.


##RSpec Plain Text Stories

This previous example gives us the basic understanding of how a Story works and is structured, but what about the RSpec plain text stories, how do I create one and how are they structured?

The goal of a Plain Text Story is to extract the Steps into a separate file and write the Story in such a way that it looks just like plain text with no Ruby code.  In fact it is possible to name the Story file such that it does not even have the **.rb** file extension.

However by extracting the Steps from the Story we will require a helper file to bind the Story to the Steps.  Therefore a plain text Story file will consist of four parts:


*  the **Story**
*  **Steps** that the Story undertakes
*  **Components** to be tested, described by the Story
*  **Helper** file to bind the Story to the Steps

There isn't any hard and fast rules about how you should structure the file system with respect to Stories and Steps, however best practice seems to describe a **stories** directory and a **steps** subdirectory.

    stories +
            + steps

Story files are created without the **.rb** file extension and are placed in the stories directory and consequently a Step file is created with the same name as the Story file, but with **_step.rb** extension.
Helper files can be put in the stories directory, and should have the same name as the story file but this time with the **.rb** file extension.

Create a Story file with the following file name, **bank**

    This is a story about an transferring money between bank accounts. 
    Note that the text up here above the Story: declaration won't be 
    processed, so you can write whatever you wish!

    Story: transfer to cash account
      As a savings account holder
      I want to transfer money from my savings account
      So that I can get cash easily from an ATM

      Scenario: savings account is in credit
        Given my savings account balance is '100'
        And my cash account balance is '10'
        When I transfer '20'
        Then my savings account balance should be '80'

      Scenario: savings account is overdrawn
        Given my savings account balance is '-20'
        And my cash account balance is '10'
        When I transfer '20'
        Then my savings account balance should be '-40'
        
Now create a steps file with the following name and place it in the steps directory, **bank_steps.rb**
{% highlight ruby %}
require 'rubygems'
require 'spec/story'
require 'spec/story/extensions/main'
require File.join(File.dirname(__FILE__), "../account")

steps_for(:bank) do
  Given("my savings account balance is '$savings'") do |savings|
    @savings_account ||= Account.new(savings)
  end
end

steps_for(:bank) do
  Given("my cash account balance is '$cash'") do |cash|
    @cash_account ||= Account.new(cash)
  end
end

steps_for(:bank) do
  When("I transfer '$amount'")  do |amount|
     @savings_account.transfer_to(@cash_account, amount)
  end
end

steps_for(:bank) do
  Then("my savings account balance should be '$expected_amount'") do |expected_amount| 
     @savings_account.balance.should == expected_amount.to_f
  end
end
{% endhighlight %}
The helper file by default is given the same name as the Story file but this time **with the .rb** extension.  Here is an example of such a file, called **bank.rb**.
{% highlight ruby %}
#!/usr/bin/env ruby
require 'rubygems'
require 'spec'
require 'spec/story/runner'
require 'account'

# require all the steps files so that we can link the story to them
Dir[File.join(File.dirname(__FILE__), "steps/*.rb")].each do |file|
  require file
end

# execute the steps in the steps file
with_steps_for :bank do
  run File.expand_path(__FILE__).gsub(".rb","")
end
{% endhighlight %}
Now to run this story all you have to do is from the Apple **Terminal.app** navigate to the stories directory and execute the following Ruby command:

    ruby bank.rb
    
If all is successful you should see the following output:

    requiring files ./steps/addition_steps.rb
    requiring files ./steps/bank_steps.rb
    Running 2 scenarios

    Story: transfer to cash account

      As a savings account holder
      I want to transfer money from my savings account
      So that I can get cash easily from an ATM

      Scenario: savings account is in credit

        Given my savings account balance is '100'
        And my cash account balance is '10'

        When I transfer '20'

        Then my savings account balance should be '80'

      Scenario: savings account is overdrawn

        Given my savings account balance is '-20'
        And my cash account balance is '10'

        When I transfer '20'

        Then my savings account balance should be '-40'

    2 scenarios: 2 succeeded, 0 failed, 0 pending

I hope this gives you a good introduction and a basis for understanding how RSpec Stories work.  Finally here are a list of links which may help you further:

*  [An introduction to RSpec - Part I](http://blog.davidchelimsky.net/articles/2007/05/14/an-introduction-to-rspec-part-i)

*  [An introduction to RSpec Stories - Part I](http://blog.davidchelimsky.net/articles/2007/10/21/story-runner-in-plain-english)

*  [An introduction to RSpec Stories - Part II](http://blog.davidchelimsky.net/articles/2007/10/22/plain-text-stories-on-rails), although the *run_story* method now seems to be depreciated in the source code.

*  [An introduction to RSpec Stories - Part III](http://blog.davidchelimsky.net/articles/2007/10/25/plain-text-stories-part-iii)

*  [Quick overview of stories](http://times.usefulinc.com/2008/01/12-rspec-stories)

*  [RSpec Story Runner Creation](http://blog.davidchelimsky.net/articles/2007/10/21/story-runner-in-plain-english)

*  [Blog describing creating a story and steps](http://www.tomtenthij.co.uk/2008/1/25/rspec-plain-text-story-runner-on-a-fresh-rails-app)

*  [Clear introduction to stories](http://www.vaporbase.com/postings/Beginners_Guide_to_Rspec_on_Story_Runner)


