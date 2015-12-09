---
layout: post
title: Rails开发环境常用配置
---
列举了Rails开发常用的gem以及初始化过程
<!--more-->

###Rails开发环境常用配置 
Rails开发中除了初始化后，Gemfile中自动包含的gem，常用的还有：
* rspec-rails
* capybara
* shoulda-matcher
* guard-rspec
* guard-livereload
* database_cleaner
* factory_girl_rails
等等。

首先在Gemfile中添加如下代码：

```ruby
   group :developement, :test do
     gem 'rspec-rails'
     gem 'capybara'
     gem 'shoulda-matcher'
     gem 'guard-rspec'
     gem 'guard-livereload'
     gem 'database_cleaner'
     gem 'factory_girl_rails'
   end
 ```
 
 保存， 然后运行：  
 `$ bundle`
 
 然后，初始化rspec
 
 ```ruby
   rails generate rspec:install
   
   #或者
   
   rails g rspec:install
 ```
 
 生成以下文件：
 
* .rspec
* spec/spec_helper.rb
* spec/rails_helper.rb

然后，初始化Guardfile

`$ guard init`

提示：
    
    11:31:04 - INFO - Writing new Guardfile to /your/app/path/Guardfile
    11:31:04 - INFO - livereload guard added to Guardfile, feel free to edit it
    11:31:05 - INFO - rspec guard added to Guardfile, feel free to edit it


然后添加__capybara__到rails_helper.rb

`require 'capybara/rails'`

添加shoulda-matcher到rails_helper.rb文件中  
`require 'shoulda/matchers'`

添加配置文件到 **spec/support/factory_girl.rb**

```ruby
# spec/support/factory_girl.rb
RSpec.configure do |config|
  # 包含这行，以便省略FactoryGirl前缀
  config.include FactoryGirl::Syntax::Methods
  
  # 设置factory_girl验证
  config.before(:suite) do
    begin
      DatabaseCleaner.start
      FactoryGirl.lint
    ensure
      DatabaseCleaner.clean
    end
  end
end
```
使用方法：
   `rails g factory_girl:model ModelName`   
rspec会自动查找`spec/factory_girl.rb`或  
`spec/factory_girl/*.rb`  
  
   
  
配置**database_cleaner**

```ruby
RSpec.configure do |config|

  config.before(:suite) do
    DatabaseCleaner.strategy = :transaction
    DatabaseCleaner.clean_with(:truncation)
  end

  config.around(:each) do |example|
    DatabaseCleaner.cleaning do
      example.run
    end
  end

end
```

至此，一个基本的rails测试环境基本搭建完毕。


