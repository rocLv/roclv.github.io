---
layout: post
title: 远程调试Rails
---

本文分享如何使用Byebug远程Rails。

<!--more-->

首先在Gemfile里包含byebug

```ruby
# Gemfile

...
group :development do
  gem 'byebug'
end

...
```

然后添加config/initializes/byebug.rb
```ruby
if Rails.env.development?
  Byebug.start_server 'localhost', ENV.fetch("BYEBUG_SERVER_PORT", 1048).to_i
end
```

然后在需要调试的地方添加`byebug`，在命令行启动
```
bundle exec byebug -R localhost:1048
```


