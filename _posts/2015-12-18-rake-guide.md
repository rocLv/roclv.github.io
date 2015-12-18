---
layout: post
title:  rake guide
---
# 4 days Rake Guide
* Day 1: Introduction to Rake
* Day 2: Dependencies
* Day 3: Rails and Rake
* Day 4: Use Rake to build project
<!--more-->
# The 4 days Rake Guide

## Day 1: Introduction to Rake

### What is rake?
Rake is a Rubygem written by Jim Weirich. He built the ruby version of make (the C build tool) and over time Rake became a popular tool to create command line Ruby applications and automation scripts. It has an easy to follow DSL and some very powerful features like task dependencies, but more on these later.

### What can I use Rake for?
If you know Ruby on Rails you use Rake very often because most of the command line Rails features are Rake tasks. Namely rake db:migrate, rake test etc. If you need a custom command line script for your Rails application (running an import, periodically updating something in the database) Rake is your friend.
Also anytime you want to automate something with a script, Rake is a very powerful tool to rely on.




### Install rake
Rake is distributed as a Rubygem so all you need to do is enter gem install rake in your console to install it.

### Rakefile
Rake reads the task from a Rakefile and in additionally you can separate your tasks into multiple files and put them into the rakelib folder with the .rake extension. Let’s create a Rakefile with a simple task:

```ruby
# Rakefile
desc "Hello world"
task :hello_world do
  puts "Hello world!"
end
```
Now we know how to make 'Hello world' with Rake. Let's break down this code. First we give a description to the task than we set the name of the task to a symbol(we could you strings too) than passing a block and Rake will evaluate that block when we call this task.

![](https://gallery.mailchimp.com/6f2ca41e3d7b84333e7f7bbe8/images/image1.jpg)

Let's call the task:
```
$ rake hello_world
Hello world!
```
If you enter `rake -T` in your console you will get a list of available tasks:
```
$ rake -T
rake hello_world  # Hello world
```
The Rakefile is basically a ruby file so you can write any ruby code inside.

### Task types
Rake has four types of tasks:

* `task` is a generic task
* `directory` creates a directory
* `file` creates a file with the result of the block we pass
* `multitask` calls its dependencies in parallel

That's it for today, tomorrow you will learn about dependencies.

## Day 2: Dependencies


Today I will teach you how Rake handles the dependencies. I will use code examples but instead of repeating the whole code everytime I will just show you the important parts, then at the bottom of the email you can view the full example.
Rake makes it easy to define dependent tasks. Let's say we want to import a dataset from a remote source into our application. We want to make separate tasks for importing the categories, products and the orders so we can run those bits separately:

```ruby
desc "Import categories"
task :categories do
  puts "import categories"
end

desc "Import products"
task :products do
  puts "import products"
end

desc "Import orders"
task :orders do
  puts "import orders"
end
```
So far so good but we need to get the remote data before each of these tasks so we will define another task and set that as a dependency for these:

```ruby
desc "Download data"
task :download do
  puts "download data from source"
end

desc "Import categories"
task :categories => :download do
  puts "import categories"
end

desc "Import products"
task :products => :download do
  puts "import products"
end

desc "Import orders"
task :orders => :download do
  puts "import orders"
end
```

![dependency](https://gallery.mailchimp.com/6f2ca41e3d7b84333e7f7bbe8/images/image2.jpg)

As you see you can set a dependency with the hashrocket and you can pass one task or an array of tasks. If you call rake with the -P switch it will display the dependency tree:

```
$ rake -P
rake categories
    download
rake download
rake orders
    download
rake products
    download
```


In our example, when we import the products, we want to make sure we have the latest categories so let's make the products task depend on categories:
```ruby
desc "Download data"
task :download do
  puts "download data from source"
end

desc "Import categories"
task :categories => :download do
  puts "Import categories"
end

desc "Import products"
task :products => :categories do
  puts "Import products"
end

desc "Import orders"
task :orders => :download do
  puts "import orders"
end
```

This looks great but we would like to have a task to run a full import too so let's make a task for that:

```ruby
desc "Import everything"
task :all => [:categories, :products, :orders] do
  puts "Imported everything"
end
```

![array of dependency](https://gallery.mailchimp.com/6f2ca41e3d7b84333e7f7bbe8/images/image3.jpg)

You might think: "But wait... this will run download 3 times and categories 2 times, won't it?" Nope. Rake disables a task after the first invocation. If you want to run a dependent task even if it is already invoked you can reenable it.
If you call the task you should see this result:

```
$ rake all
download data from source
import categories
import products
import orders
Imported everything

```
If you want to import the products only:

```
$ rake products
download data from source
import categories
import products
```
If you need to `reenable` a task:

```ruby
desc "Import categories"
task :categories => :download do
  puts "Import categories"
  Rake::Task["categories"].reenable
end
```

Note that it imported the categories first because products depends on categories.
We can also set a default task with rake so when you call only rake it will run the default task. Let's set the all task to be the default:

`task :default => [:all]`

As I promised here is how the Rakefile should look now:

```ruby
desc "Download data"
task :download do
  puts "download data from source"
end

desc "Import categories"
task :categories => :download do
  puts "Import categories"

end

desc "Import products"
task :products => :categories do
  puts "Import products"
end

desc "Import orders"
task :orders => :download do
  puts "import orders"
end

desc "Import eveything"
task :all => [:categories, :products, :orders] do
  puts "Imported everything"
end

task :default => [:all]
```

That's it for today. Tomorrow we will look at how Rails uses Rake and how can we add custom Rake tasks to a Rails application.

## Day 3: Rails and rake
As I already mentioned Rails uses Rake for various tasks like running the database migrations. Today we are going to look at these tasks.
If you enter rake -T you will get a list of Rake tasks but this only displays the ones with a description, so the ones intended to be internal are not on this list. If you want to see those too you can use the -A switch:
```
$ rake -T -A
rake about                      # List versions of all Rai..
rake assets:clean[keep]         # Remove old compiled assets
rake assets:clobber             # Remove compiled assets
rake assets:environment         # Load asset compile enviro.
rake assets:precompile          # Compile all the assets ...
rake cache_digests:dependencies # Lookup first-level depen..
rake db:_dump                   #
rake db:charset                 #
...
```

If you want to make a custom Rake task in your Rails application you can create a file with the .rakeextension in the lib/tasks folder. For instance if you would like to make that import inside a Rails application, you would add the code from yesterday to lib/tasks/import.rake.
One important part we haven't talked about yet is namespacing. There might be another task callled download in your Rails application which would cause some unexpected results. Important to note here the fact, if Rake finds a task definition for an already defined task it will extend not override it. To make sure our tasks are safe and also to make them nicely grouped we will move them into a namespace:

```ruby
# lib/tasks/import.rake
namespace :import
  desc "Download data"
  task :download do
    puts "download data from source"
  end

  desc "Import categories"
  task :categories => :download do
    puts "import categories"
  end

  desc "Import products"
  task :products => :categories do
    puts "import products"
  end

  desc "Import orders"
  task :orders => :download do
    puts "import orders"
  end

  desc "Import eveything"
  task :all => [:categories, :products, :orders] do
    puts "Imported everything"
  end
end
```

From now on we can call the tasks with the colon syntax: `rake import:all`. When you write a task which interacts with Rails you should set the environment task to a dependency. Since the download task is called in each case we can make that depending on environment:

```ruby
...
task :download => :environment do
  puts "download data from source"
end
...
```
This task basically loads your app for you in the environment you had set.
It is good to know Rails sets eager_load to false in this task so if you want to use multiple threads in a task it is better to not use this task as a dependency, rather just load the environment yourself:

```ruby
...
# if you use multiple threads
task :download do
  Rails.application.require_environment!
  puts "download data from source"
end
...
```

This was a short one but tomorrow we will do some really interesting stuff when I will share you how I build this newsletter with the help of Rake.
Here is the full Rakefile of what we have done today:

```ruby
# lib/tasks/import.rake
namespace :import
  desc "Download data"
  task :download => :environment do
    puts "download data from source"
  end

  desc "Import categories"
  task :categories => :download do
    puts "import categories"
  end

  desc "Import products"
  task :products => :categories do
    puts "import products"
  end

  desc "Import orders"
  task :orders => :download do
    puts "import orders"
  end

  desc "Import eveything"
  task :all => [:categories, :products, :orders] do
    puts "Impor#Imported everything"
  end
end

```

## Day 4: Use Rake to build a project

Taking it to the next level
As I already mentioned Rake is intended to be a build tool, but it is used to do a lot of other things and many Rake users don't even know the features makes Rake a powerful build system.
Actually I am using Rake to build this course. I write all the content in markdown, and I have a Rake task which converts that to HTML.
I have a directory structure like this:

>
    source
      - day1.md
      - day2.md
      - day3.md
      - day4.md
      ...
      Rakefile


As you see, I store my markdown files in the source folder, and I want to have an output folder where I want my HTML files to be created. Rake has a directory method which I can use:

`directory "output"`
Next, I will create a Filetask, which is a bit different from normal tasks. Filetasks doesn't trigger all the time, only if the target file doesn't exist or the source file is changed since the target was created. Because of this Rake will save some time on rebuilds. Let's see some code for this:

```ruby
# Rakefile
directory "output"

desc "build day1"
file "output/day1.html" => ["output", "source/day1.md"] do |task|
  rendered = redcarpet.render(
    File.read(task.prerequisites.first)
  )
  File.new(task.name, 'w+').write(rendered)
  puts "Built #{task.name}"
end
```
When you define a Filetask, the name of the task is the target file and you can pass an array of prerequisites with the hashrocket and do the work in the block. The prerequisites are the `output` folder and the source file in this case. With the file task I have access to the [Task](http://rake.rubyforge.org/Rake/Task.html) object in the block which holds the name of the task and the prerequisites. I print the name of the task at the end of the block which helps to see when the file is actually built. If I call `rake output/day1.html` twice in a row, the second time it won't print the output because it will not generate the file again. Two more things to note about Filetasks:

* the naming convention is to use strings but you can use symbols if you would like
* Filetasks ignore the namespaces

If I would go this way I should repeat this code for each day and I am too lazy to do that. Instead I use Rake's FileList and I create an array of arrays with the target and the source:

```ruby
# Rakefile
directory "output"

source_files = FileList['source/*.md']
target_files = source_files.pathmap("output/%n.html")

target_files.zip(source_files).each do |target, source|
  desc "build #{target}"
  file target => ["output", source] do |task|
    rendered = redcarpet.render(
      File.read(task.prerequisites.first)
    )
    File.new(task.name, 'w+').write(rendered)
    puts "Built #{task.name}"
  end
end
```
If I list the tasks now I get the following result:

```
$ rake -T
rake output/day1.html  # build output/day1.html
rake output/day2.html  # build output/day2.html
rake output/day3.html  # build output/day3.html
rake output/day4.html  # build output/day4.html
```
Since I don't want to trigger these individually, I have a task to build all of the source files:

```ruby
desc "Build all files"
task :build => target_files
```
As you I see I just simply set target_files as a dependency and Rake will call all the dynamically generated tasks for me.
This isn't a bad solution but if I had hundreds of files it wouldn't be optimal. Instead Rake has another way of achieving the same result. It is called Rules.

###Rules
This feature of Rake is a little bit like method_missing in Ruby. If Rake couldn't find a Filetask it will look at the rules defined and if it finds a match, it will call it. When we define a rule we pass the target as either a string or a regular expression, and we also pass the source, but since that is dynamic we will use a lambda to get the source name from the target. Then in the block we will do the work:

```ruby
# Rakefile
directory "output"
source = lambda { |fn|
 fn.pathmap("source/%n.md")
}
rule /^output\/.*\.html/ => [source] do |task|
  rendered = redcarpet.render(File.read(task.source))
  File.new(task.name, 'w+').write(rendered)
  puts "Built #{task.name}"
end

task :build => ["output"] + target_files
```
Now we have a few lines of code which covers almost all my needs. One more thing I'd like to have is a way to get a clean state, so I will use Rake's built in clean task:
```ruby
require 'rake/clean'
CLEAN.include("output")
```

## Summary
That's all I wanted to share with you about Rake. I hope you enjoyed the series of emails and you learned a few tricks.
If you have aboutny questions of feedback about what you've read don't hesitate to contact me at greg@molnar.io.

Copyright © 2015 Gergely Molnar, All rights reserved.
