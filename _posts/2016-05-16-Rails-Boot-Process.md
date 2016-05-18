---
layout: default
title: Rails Boot Process

---
这是根据Rails Conference 2016中Xavier Noria的演讲整理而成。

这篇演讲的目标是理解Rails的启动过程：
* 理解config/boot.rb、config/environment.rb等等的作用
* Rails的组件是怎样集成起来的
* 当Rails启动的时候发生了什么

<!--more-->

Rails有以下几个命令，应用于不同的场景：

```
bin/rails server
bin/rails console

bin/rails runner

bin/rails routes
```
其他的命令大家都比较熟悉，很多人不熟悉的是`railsrunner`这个命令，（比如笔者我,汗）
    
    

    Usage: rails runner [options] [<'Some.ruby(code)'> | <filename.rb>]
    
        -e, --environment=name           Specifies the environment for the runner to operate under (test/development/production).
                                         Default: development
    
        -h, --help                       Show this help message.
    
    Examples:
        rails runner 'puts Rails.env'
            This runs the code `puts Rails.env` after loading the app
    
        rails runner path/to/filename.rb
            This runs the Ruby file located at `path/to/filename.rb` after loading the app
    
    You can also use runner as a shebang line for your executables:
        -------------------------------------------------------------
        #!/usr/bin/env /Users/wangqsh/Samples/rails5sample/bin/rails runner
    
        Product.
    
    

上面是runner的一些用法，和`ruby -e`的用法差不多

让我们打开`bin/rails`文件看看：

```
# bin/rails文件的内容

#!/usr/bin/env ruby
APP_PATH = File.expand_path('../config/application', __dir__)
require_relative '../config/boot'
require 'rails/commands'
```
第一行是shebang，在linux可以直接运行

第二行定义了APP_PATH这个常量
```ruby
APP_PATH = File.expand_path('../config/application', __dir__)
```

`__dir__`返回当前路径，然后通过`File.expand_path`返回绝对路径。

第三四行`require` boot文件和commands文件

我们看看`boot.rb`文件中的内容

```ruby
# config/boot.rb

ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../Gemfile', __dir--)
require 'bundle/setup'
```
这个文件的作用是执行`bundle setup`,安装项目需要的gem。

让我们再看看`bin/rake`文件中的内容
```ruby
# bin/rake.rb

#!/usr/bin/env ruby
require_relative '../config/boot'
require 'rake'
Rake.application.run
```
同样是先安装所需的Gem，然后启动程序。

`bin/rails` 和 `bin/rake`一样都是先执行`config/boot.rb`中的内容

我们再看看`config.ru`文件中的内容。`config.ru`是所有 rake 程序的启动文件.

```ruby
# config.ru

# This file is used by Rack-based servers to start the application.

require_relative 'config/environment'

run Rails.application
```
正如该文件所注释的一样,这个文件是所有几乎 Rake 的服务器的启动程序, 任何支持`call`
方法的函数都可以.

这个文件 `require` 了`config/environment.rb` 文件. 

```ruby
# config/environment.rb

# Load the Rails application.
require_relative 'application'

# Initialize the Rails application.
Rails.application.initialize!
```
在`environment.rb` 文件中,首先 rails 包含了 application.rb 用来配置我们的
application, 然后 rails 初始化了应用程序.

让我们再看看`config/application.rb` 中的内容:

```ruby
# config/application.rb

require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module Rails5sample
  class Application < Rails::Application
    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.
  end
end
```

到此为止  我们的Rails程序已经启动了. 让我们再概括一下Rails 程序的启动过程:

* 为我们的应用程序的依赖库定义了加载的路径(Bundle.setup)
* 加载 rails/all
* 加载gem依赖库( Bundle.require)
* 定义了我们的应用程序类
* Rails.application.initialize!

## Rails::Railtie

Rails::Railtie 是一个提供以下功能的类:

* 当`console`, `runner` 等命令被唤起的时候, 为将要执行的代码提供钩子函数
* 定义自定义配置项的能力
* 在应用程序启动期间宣称要执行的代码的能力

Railtie 是 Rails::Railtie 的子类. 因为 Rails::Railtie
会通过`inherited`钩子方法来注册继承了 Rails::Railtie 的子类, 所以Rails知道那个
railtie被注册了.

让我们看一个例子,下面是 factory_girl 中的 Railtie:

```ruby
# factory_girl_rails/railtie.rb

module FactoryGirl
  class Railtie < Rails::Railtie

    initializer "factory_girl.set_factory_paths" do
      FactoryGirl.definition_file_paths = [
        Rails.root.join('factories'),
        Rails.root.join('test', 'factories'),
        Rails.root.join('spec', 'factories')
      ]
    end
  end
end
```

## Rails 组件通过定义 Railtie和 Rails 集成在一起.

例如:

```ruby
# active_record/railtie.rb

module ActiveRecord
  class Railtie < Rails::Railtie
    console do |app|
      if app.sandbox?
         # Load sandbox support
      end

      # Code to log SQL to STDERR
    end
  end
end
```

另外看看action_dispatch 中的 Railtie:

```ruby
# action_dispatch/raitie.rb

module ActionDispatch
  class Railtie < Rails::Railtie
    config.action_dispatch = ActiveSupport::OrderedOptions.new

    config.action_dispatch.tld_length = 1
  end
end
```

下面是`all.rb` 中的内容

```ruby

require "rails"

%w(
  active_record/railtie
  action_controller/railtie
  action_view/railtie
  action_mailer/railtie
  active_job/railtie
  action_cable/engine
  rails/test_unit/railtie
  sprockets/railtie
).each do |railtie|
  begin
    require "#{railtie}"
  rescue LoadError
  end
end
```

## 这其中的首要原则是应用程序和 Rails 组件需要解耦.

## Railtie 知道它们生存在父应用程序中.

## 一个普通的 Rails 应用程序有15个 Railtie.

* I18n::Railtie
* ActiveSupport::Railtie
* ActionDispatch::Railtie
* ActiveModel::Railtie
* ActionView::Railtie
* ActionController::Railtie
* ActiveRecord::Railtie
* GlobalID::Railtie
* ActiveJob::Railtie
* ActionMailer::Railtie
* Sprockets::Railtie
* JBuilder::Railtie
* WebConsole::Railtie
* Sass::Rails::Railtie
* Rails::TestUnitRailtie

# Lazy Loading

```ruby
module ActiveRecord
  autoload :Base
end
```

## ActiveSupport.on_load

## ActiveSupport.run_oad_hooks(:active_record, ActiveRecord::Base)

# Rails Engine

Rails::Engine < Rails::Railtie

Engine有额外的配置和预配置,下面是所有 Engine 都包含的一些初始化配置:
* set_load_path, 用来配置加载路径
* set_autoload_paths, 配置自动加载的路径
* add_routing_paths, 配置重新加载器的路由
* add_locales, 为 i18n 配置locales
* add_view_paths
* load_environment_config, 加载config/environments/#{Rails.env}.rb
* prepend_helpers_ath, 为非独立的引擎或应用程序配置helper 的路径
* load_config_initializers, 按照字典顺序加载config/initializers
* engine_blank_point, 什么也不做, 是保留的技术 hook


一个普通的 Rails 应用程序包括四个 Engine:
* ActionCable::Engine
* Coffee::Rails::Engine
* Jquery::Rails::Engine
* Turbolinks::Engine


# Application 是 Rails::Application 的子类, 也是单例类.

当这个单例类被初始化的时候,会激发 :before_configuration方法.

Application 会执行四组 initializer:
* 继承于 Engine 的
* "Bootstrap" 组
  * load_environment_hook, 除了发出这个组开始运行的信号外其他什么也不做
  * load_active_support, 加载 active_support/all, 如果
    config.active_support.bare 值为 true 时什么也不做
  * set_eager_load
  * initialize_logger,  设置 Rails.logger
  * initialize_cache, 设置 Rails.cache
  * initialize_dependency_mechanism, 依据 config.cache_classes的值设置:load
    或:require作为自动加载的机制
  * bootstrap_hook, 激发:before_initialize

* Railtie and Engine
  ** 总共有94个initializer
* "完成"组
   * add_generate_templates, 为 generate 设置 lib/templates
   * ensure_autoload_once_paths_as_subset,
     假如auto_load_once_paths没有包含在autoload_paths时退出
   * add_builtin_route,在开发者模式为'/'和' /rails/info'设置欢迎页面
   * build_middleware_stack,配置Rack 应用和中间件
   * define_main_app_helper, 为 Engine 定义`main_app`,
     为了进入父应用中的路由辅助方法
  * add_to_prepare_blocks, 在重载器里设置`to_prepare`块
  * run_prepare_callbacks, 执行那些块
  * eager_load!, 假如config.eager_load 的值为true, 激发
    before_eager_load和应用的 preload,
  * finisher_hook, 激发:after_initialize
  ...

# 启动过程摘要
* 为依赖库定义加载路径( Bundle.setup )
* 加载 Rails 组件的railtie
* 加载 gem依赖库( Bundle.require )
* 定义应用程序类
* 定义按字典顺序排序的加载路径
* 加载 config/environments/#{Rails.env}.rb
* Eager Load(按需)
* 加载路由
* 按需运行命令钩子



