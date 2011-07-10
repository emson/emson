require 'rubygems'
require 'rake'
require 'date'



desc 'create a new draft post:  TITLE="My New Post"'
task :post do
  title = ENV['TITLE']
  slug = "#{Date.today}-#{title.downcase.gsub(/[^\w]+/, '-')}"

  file = File.join(
    File.dirname(__FILE__),
    '_posts',
    slug + '.markdown'
  )

  File.open(file, "w") do |f|
    f << <<-EOS.gsub(/^    /, '')
    ---
    layout: post
    title: #{title}
    published: false
    categories:
    ---

    EOS
  end

  system ("#{ENV['EDITOR']} #{file}")
end


desc 'List all draft posts'
task :drafts do
  puts `find ./_posts -type f -exec grep -H 'published: false' {} \\;`
end



