--- 
layout: post
title: Code Kata Four Exercise - A Ruby Solution
wordpress_id: 101
wordpress_url: http://blog.emson.co.uk/?p=101
---
#Code Kata Four Exercise - A Ruby Solution
I recently did [code kata 4 (data munging)](http://codekata.pragprog.com/2007/01/kata_four_data_.html), and here is my solution to the problem. Like anything it can be improved but I think it is relatively simple to understand and yet flexible enough to work for a number of different formatted **.dat** files.

The premiss is to write a program that will work out the deltas ( difference ) between two columns in **.dat** files and return the lowest values.

These **.dat** files have different numbers of columns and these columns can be in different order. The two **.dat** files used for this program supplied weather temperature ranges and football (soccer) result scores.

Anyway here is my solution in the Ruby language and a description of how it works:
{% highlight ruby %}

# data_processor.rb

module DataProcessor

  def process_dat(filename, regex=/^\s*\d+\.?\s+/)
    items = []
    IO.foreach(filename) do |line|
      if line =~ regex
        num, max, min = yield line
        delta = max.to_i - min.to_i
        items << [num, delta] if items.size == 0
        current_items = items[0][1]
        if delta <= current_items
          items.clear if delta < current_items
          items << [num, delta]
        end
      end
    end  
    items
  end

end

if __FILE__ == $0

  include DataProcessor

  puts "-------- Weather  --------"
  list = process_dat('weather.dat') { |line| tokens = line.split(' ') }
  list.each { |item| puts "Lowest Day:#{item[0]} Delta:#{item[1]}"}

  puts "\n-------- Football --------"
  list = process_dat('football.dat') do |line| 
    tokens = line.split(' ')
    [tokens[1], tokens[6], tokens[8]]
  end
  list.each { |item| puts "Team:#{item[0]} Delta:#{item[1]}"}
end
    
{% endhighlight %}

The code above was created as a **module** as this will allow the **process_dat** method to be mixed-in to other objects, allowing these objects to execute the code without having to instantiate another class to get this functionality.

The **process_dat** method takes a filename and an optional regular expression. The regular expression (regex) is used to find the data in the text file for processing. In this case any line that starts with 0 or more spaces followed by at least one number and then an optional '.' after that number followed by at least one space.  This regular expression could have been used to extract the data from each row as well - but it actually became much easier to simply split each row and tokenise it around a space, as we are processing space separated files.

Using the **IO.foreach** method will iterate over each line of the **.dat** file allowing the logic to process that line. This is much more memory efficient, as only one line is loaded into memory at each iteration of the loop - thus allowing this method to process very large data files.

Next the method checks to see if the line matches the regex (`if line =~ regex`) if it does it can be processed.

Processing each line is handled by a Ruby block, (as a **yield** statement is used), in the "weather.dat" case the block simply splits the line around a white space. In the "football.dat" case the block splits the line around the white space but re orders the returned columns. Incidentally it is useful to know that if the number of tokens in the array exceeds the number of possible assignments (`num, max, min = yield line`, where `yield line` returns a 10 item array for instance) Ruby conveniently ignores the other values after assigning the first lot.

The next couple of lines simply calculate the delta and assigns it to a temporary array (`items`) if items is empty. The items array simply holds the lowest values as a collection of lowest values with the same value.
Note that the items array is an array of arrays. The item in the items array consists of a number and a delta.

Finally we get the lowest item from the items array and check to see if the current line delta is lower if it is, clear the items array and add this new item.  If it equals the same value simply add this item to the items array.
Thus the items array will contain the same lowest values.

I hope you find this helpful - if you have an alternative solution, why don't you add it to the comments at the bottom.

