---
layout: post
title: God mannual
---

**Ruby进程监控框架**

**服务器监控的更好解决方案**

God是一个用Ruby写成的，容易配置，容易扩展的服务器监控框架。

保持你的服务器的程序和任务运行应该是你部署过程中简单的一部分。God的目标做最简单、功能最强的监控应用程序。
<!--more-->

Tom Preston-Werner
<tom@mojombo.com>

Google Group:[http://groups.google.com/group/god-rb](http://groups.google.com/group/god-rb)
![alt GOD](http://godrb.com/images/banner.jpg "god")
### 系统特性
* 配置文件为Ruby程序
* 可以容易地用Ruby自定义条件
* 支持基于条件的poll和event
* 不同的poll条件可以有不同的间隔
* 集成事件通知系统（可扩展）
* 容易地控制非守护脚本

### 安装
最好的方法是通过rubygems安装：
          
```
     $[sudo] gem install god
```

### 系统需求
God目前仅仅在Linux（Kernel 2.6.15+）,BSD, 和Darwin系统上运行。暂时没有支持Windows系统的计划。在Linux系统上基于条件的事件监控需要加载cn（connector）核心模块或者编译进核心，god需要以*root*权限运行。

以下系统已经测试过。欢迎帮忙测试其他系统！
* Darwin 10.4.10
* RedHat Fedora 6-15
* Ubuntu Dapper（no events）
* Ubuntu Feisty
* CentOS 4.5（no events）, 5, 6

### 快速开始
备注： 这个快速向导需要0.12.0以后的版本。你可以检查你的版本：

```
  $ god --version
```

最简单的理解god怎么运行的方法是测试一个简单的例子。为了让你快速上手，我将给你展示怎么保持一个很小的服务运行。

新建一个目录，然后写一个简单的服务器脚本。让我们给它命名为simple.rb:

```ruby
loop do
  puts 'Hello'
  sleep 1
end
```

现在我们将写一个god配置文件，告诉god关于我们的进程。把它和simple.rb放在同一个文件夹，命名为simple.god:


```ruby
God.watch do |w|
  w.name = "simple"
  w.start = "ruby /full/path/to/simple.rb"
  w.keepalive
end
```
这是最简单的god配置文件。我们以宣布一个God.watch块开始。一个watch在god里代表一个我们想要watch和控制的进程。每个watch必须有一个唯一的名字和一个告诉god怎么启动进程的命令。*keepalive*的声明告诉god保持这个进程alive。假如god启动时这个进程没有运行，god将会启动它。假如进程不响应，god就会重新启动它。

在这个例子里，simple进程在前端运行，所以god会照看该进程，保持跟踪这个进程的PID。如果可能，最好要god为我们启动进程，这样我们就不必担心指定和保持跟踪PID的文件。后面我们将看见不能在前景运行或者需要指定进程PID的情况下怎样管理。

为了运行god，我们使用参数-c给它提供一个参数。通过参数-D就可以让god在前端运行，能让我们看见发生了什么。

```
 $ god -c path/to/simple.god -D
```

god可以通过两种方式来监控你的进程。第一个和最好的一个是方法是用event。不是每个系统都支持，但是如果系统支持的话会自动使用event。通过event，god会立即知道一个进程是否存在。对那些系统没有event支持的，god使用polling机制。这个部分的整个输出如下：

启动god后，你回看见一些例如以下的输出：

```
# Events

I [2011-12-10 15:24:34]  INFO: Loading simple.god
I [2011-12-10 15:24:34]  INFO: Syslog enabled.
I [2011-12-10 15:24:34]  INFO: Using pid file directory: /Users/tom/.god/pids
I [2011-12-10 15:24:34]  INFO: Started on drbunix:///tmp/god.17165.sock
I [2011-12-10 15:24:34]  INFO: simple move 'unmonitored' to 'init'
I [2011-12-10 15:24:34]  INFO: simple moved 'unmonitored' to 'init'
I [2011-12-10 15:24:34]  INFO: simple [trigger] process is not running (ProcessRunning)
I [2011-12-10 15:24:34]  INFO: simple move 'init' to 'start'
I [2011-12-10 15:24:34]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-10 15:24:34]  INFO: simple moved 'init' to 'start'
I [2011-12-10 15:24:34]  INFO: simple [trigger] process is running (ProcessRunning)
I [2011-12-10 15:24:34]  INFO: simple move 'start' to 'up'
I [2011-12-10 15:24:34]  INFO: simple registered 'proc_exit' event for pid 23298
I [2011-12-10 15:24:34]  INFO: simple moved 'start' to 'up'

# Polls

I [2011-12-07 09:40:18]  INFO: Loading simple.god
I [2011-12-07 09:40:18]  INFO: Syslog enabled.
I [2011-12-07 09:40:18]  INFO: Using pid file directory: /Users/tom/.god/pids
I [2011-12-07 09:40:18]  INFO: Started on drbunix:///tmp/god.17165.sock
I [2011-12-07 09:40:18]  INFO: simple move 'unmonitored' to 'up'
I [2011-12-07 09:40:18]  INFO: simple moved 'unmonitored' to 'up'
I [2011-12-07 09:40:18]  INFO: simple [trigger] process is not running (ProcessRunning)
I [2011-12-07 09:40:18]  INFO: simple move 'up' to 'start'
I [2011-12-07 09:40:18]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-07 09:40:19]  INFO: simple moved 'up' to 'up'
I [2011-12-07 09:40:19]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 09:40:24]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 09:40:29]  INFO: simple [ok] process is running (ProcessRunning)
```

你可以看见god启动了，注意到simple没有在运行，启动它，然后每隔5分钟检查一下确保simple运行正常。假如你想看见god的魔力，kill simple的进程。你会发现类似如下的输出：

```
# Events

I [2011-12-10 15:33:38]  INFO: simple [trigger] process 23416 exited (ProcessExits)
I [2011-12-10 15:33:38]  INFO: simple move 'up' to 'start'
I [2011-12-10 15:33:38]  INFO: simple deregistered 'proc_exit' event for pid 23416
I [2011-12-10 15:33:38]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-10 15:33:38]  INFO: simple moved 'up' to 'start'
I [2011-12-10 15:33:38]  INFO: simple [trigger] process is running (ProcessRunning)
I [2011-12-10 15:33:38]  INFO: simple move 'start' to 'up'
I [2011-12-10 15:33:38]  INFO: simple registered 'proc_exit' event for pid 23601
I [2011-12-10 15:33:38]  INFO: simple moved 'start' to 'up'

# Polls

I [2011-12-07 09:54:59]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 09:55:04]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 09:55:09]  INFO: simple [trigger] process is not running (ProcessRunning)
I [2011-12-07 09:55:09]  INFO: simple move 'up' to 'start'
I [2011-12-07 09:55:09]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-07 09:55:09]  INFO: simple moved 'up' to 'up'
I [2011-12-07 09:55:09]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 09:55:14]  INFO: simple [ok] process is running (ProcessRunning)
```
保持进程启动是好的，但是假如能够保证我们的进程表现良好，当资源超过我们的设置，重新启动进程将更好。通过添加一点条件，当内存或者CPU超过我们设定的限制，我们能够容易地让我们的进程重启。编辑simple.god配置文件如下：

```ruby
God.watch do |w|
  w.name = 'simple'
  w.start = "ruby /full/path/to/simple.rb"
  w.keepalive( :memory_max => 150.megabytes,
               :cpu_max => 50.percent)
end
```
这里我在keepalive命令中使用了 :memory_max 选项。 现在，假如进程的内存用量超过150M， god就会重启他。相似地，通过设置 :cpu_max, 假如CPU的使用超过50%，god也会重启它。 默认这些属性每隔30秒检查一次，假如五个条件中满足三个，则会执行。这防止了进程因为暂时的资源峰值导致重启。

为了测试这个特性，修改你的simple.rb服务器脚本使得引起内存泄露：

```ruby
data = ''
loop do
  puts 'Hello'
  100000.times { data << 'x' }
end
```
按Ctrl-C结束god。注意到你的simple进程依然在运行。用刚才的方式再次启动god。现在代替了启动simple进程，god监测到simple运行，简单的切换到up状态。

```
# Events

I [2011-12-10 15:36:00]  INFO: Loading simple.god
I [2011-12-10 15:36:00]  INFO: Syslog enabled.
I [2011-12-10 15:36:00]  INFO: Using pid file directory: /Users/tom/.god/pids
I [2011-12-10 15:36:00]  INFO: Started on drbunix:///tmp/god.17165.sock
I [2011-12-10 15:36:00]  INFO: simple move 'unmonitored' to 'init'
I [2011-12-10 15:36:00]  INFO: simple moved 'unmonitored' to 'init'
I [2011-12-10 15:36:00]  INFO: simple [trigger] process is running (ProcessRunning)
I [2011-12-10 15:36:00]  INFO: simple move 'init' to 'up'
I [2011-12-10 15:36:00]  INFO: simple registered 'proc_exit' event for pid 23601
I [2011-12-10 15:36:00]  INFO: simple moved 'init' to 'up'

# Polls

I [2011-12-07 14:50:46]  INFO: Loading simple.god
I [2011-12-07 14:50:46]  INFO: Syslog enabled.
I [2011-12-07 14:50:46]  INFO: Using pid file directory: /Users/tom/.god/pids
I [2011-12-07 14:50:47]  INFO: Started on drbunix:///tmp/god.17165.sock
I [2011-12-07 14:50:47]  INFO: simple move 'unmonitored' to 'up'
I [2011-12-07 14:50:47]  INFO: simple moved 'unmonitored' to 'up'
I [2011-12-07 14:50:47]  INFO: simple [ok] process is running (ProcessRunning)

```

为了让我们的simple服务运行，我们重新启动simple：

```
 $ god restart simple 
```

通过日志你可以看见god结束了simple进程并重新启动了：

```
# Events

I [2011-12-10 15:38:13]  INFO: simple move 'up' to 'restart'
I [2011-12-10 15:38:13]  INFO: simple deregistered 'proc_exit' event for pid 23601
I [2011-12-10 15:38:13]  INFO: simple stop: default lambda killer
I [2011-12-10 15:38:13]  INFO: simple sent SIGTERM
I [2011-12-10 15:38:14]  INFO: simple process stopped
I [2011-12-10 15:38:14]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-10 15:38:14]  INFO: simple moved 'up' to 'restart'
I [2011-12-10 15:38:14]  INFO: simple [trigger] process is running (ProcessRunning)
I [2011-12-10 15:38:14]  INFO: simple move 'restart' to 'up'
I [2011-12-10 15:38:14]  INFO: simple registered 'proc_exit' event for pid 23707
I [2011-12-10 15:38:14]  INFO: simple moved 'restart' to 'up'

# Polls

I [2011-12-07 14:51:13]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 14:51:13]  INFO: simple move 'up' to 'restart'
I [2011-12-07 14:51:13]  INFO: simple stop: default lambda killer
I [2011-12-07 14:51:13]  INFO: simple sent SIGTERM
I [2011-12-07 14:51:14]  INFO: simple process stopped
I [2011-12-07 14:51:14]  INFO: simple start: ruby /Users/tom/dev/mojombo/god/simple.rb
I [2011-12-07 14:51:14]  INFO: simple moved 'up' to 'up'
I [2011-12-07 14:51:14]  INFO: simple [ok] process is running (ProcessRunning)

```
God现在开始报告内存和CPU的使用情况

```
# Events and Polls

I [2011-12-07 14:54:37]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 14:54:37]  INFO: simple [ok] memory within bounds [2032kb] (MemoryUsage)
I [2011-12-07 14:54:37]  INFO: simple [ok] cpu within bounds [0.0%%] (CpuUsage)
I [2011-12-07 14:54:42]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 14:54:42]  INFO: simple [ok] memory within bounds [2032kb, 13492kb] (MemoryUsage)
I [2011-12-07 14:54:42]  INFO: simple [ok] cpu within bounds [0.0%%, *99.7%%] (CpuUsage)
I [2011-12-07 14:54:47]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 14:54:47]  INFO: simple [ok] memory within bounds [2032kb, 13492kb, 25568kb] (MemoryUsage)
I [2011-12-07 14:54:47]  INFO: simple [ok] cpu within bounds [0.0%%, *99.7%%, *100.0%%] (CpuUsage)
I [2011-12-07 14:54:52]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 14:54:52]  INFO: simple [ok] memory within bounds [2032kb, 13492kb, 25568kb, 37556kb] (MemoryUsage)
I [2011-12-07 14:54:52]  INFO: simple [trigger] cpu out of bounds [0.0%%, *99.7%%, *100.0%%, *98.4%%] (CpuUsage)
I [2011-12-07 14:54:52]  INFO: simple move 'up' to 'restart
```

在最后的一行，你能看见CPU的用量已经超过了50%三次了，god重新启动了进程。god会持续地监测simple进程，只要god在运行，进程就会被一直监控。

现在，你结束god之前，让我们先通过god结束simple服务。在一个新的终端，输入以下命令：

```
 $ god stop simple 
```

你应该看见如下输出：

```
Sending 'stop' command

The following watches were affected:
   simple
 
```

在god运行的终端，你会看见发生了什么：

```
# Events

I [2011-12-10 15:41:04]  INFO: simple stop: default lambda killer
I [2011-12-10 15:41:04]  INFO: simple sent SIGTERM
I [2011-12-10 15:41:05]  INFO: simple process stopped
I [2011-12-10 15:41:05]  INFO: simple move 'up' to 'unmonitored'
I [2011-12-10 15:41:05]  INFO: simple deregistered 'proc_exit' event for pid 23707
I [2011-12-10 15:41:05]  INFO: simple moved 'up' to 'unmonitored'

# Polls

I [2011-12-07 09:59:59]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 10:00:04]  INFO: simple [ok] process is running (ProcessRunning)
I [2011-12-07 10:00:07]  INFO: simple stop: default lambda killer
I [2011-12-07 10:00:07]  INFO: simple sent SIGTERM
I [2011-12-07 10:00:08]  INFO: simple process stopped
I [2011-12-07 10:00:08]  INFO: simple move 'up' to 'unmonitored'
I [2011-12-07 10:00:08]  INFO: simple moved 'up' to 'unmonitored'

```

现在，你可以自由的按Ctrl—C退出god了。热烈祝贺！你已经测试了一遍god，也已经看见god是多么容易的让你的进程一直运行了。

不过这只是个开始。在实际应用中，*keepalive* 命令是一个方便的方法，使用了可以直接使用的更高级的事务和条件构造。你可以配置许多不同的条件，当CPU或者内存使用太多，磁盘超过下限，当一个指定的URL返回错误代码，等等。另外，你可以写自己的自定义条件，然后在配置文件里使用它。伴随着复杂的和可扩展的通知体系，可以控制许多不同的生命周期。

继续阅读找出god不同于其他监测系统的地方，以及它怎么能帮助你解决许多进程监控和控制问题。

###用Ruby写配置文件
既然你已经知道怎么使用god，让我们看看god更强大的一面吧。再说一次，最好的学习方法是通过示例。下面这个配置文件是我在gravatar.com保证mongrels运行使用的配置文件。

```ruby
RAILS_ROOT = "/Users/tom/dev/gravatar2"

%w{8200 8201 8202}.each do |port|
  God.watch do |w|
    w.name = "gravatar2-mongrel-#{port}"
    
    w.start = "mogrel_rails start -c #{RAILS_ROOT} -p #{port} \
      -P #{RAILS_ROOT}/log.mogrel.#{port}.pid -d"
    w.stop = "morgrel_rails stop -P #{RAILS_ROOT}/log/mogrel.#{port}.pid"
    w.restart = "mongrel_rails restart -P #{RAILS_ROOT}/log/mogrel.#{port}.pid"
    
    w.pid_file = File.join(RAILS_ROOT, "log/mogrel.#{port}.pid")
    
    w.behavior(:clean_pid_file)
    
    w.start_if do |start|
      start.condition(:process_running) do |c|
        c.inteval = 5.seconds
        c.running = false
      end
    end
    
    w.restart_if do |restart|
      restart.condition(:memory_usage) do |c|
       c.above = 150.megabytes
       c.times = [3, 5] # 3 out of 5 intevals
      end
      
      restart.condition(:cpu_usage) do |c|
        c.above = 50.percent
        c.times = 5
      end
    end
    
    #lifecycle
    w.lifecycle do |on|
     on.condition(:flapping) do |c|
       c.to_state = [:start, :restart]
       c.times = 5
       c.within = 5.minute
       c.transition = :unmonitored
       c.retry_in = 10.minutes
       c.retry_times = 5
       c.retry_within = 2.hours
     end
    end
   end
  end
      
```

新手是不是一下子吓蒙了？所以我把它拆散了解释每一部分的作用

```ruby
 RAILS_ROOT = "/var/www/gravatar2/current"
```

在这里我设置了一个常量，用于整个文件。保持RAILS_ROOT的值是一个常量使得脚本很容易适合其他应用。因为这个配置文件是Ruby代码，我可以设置任何变量或者常量，这样可以使得配置更简单，更容易工作。

```ruby
%w{8200 8210 8202}.each do |port|
  ...
end
```

因为配置文件是用Ruby写的，我们能构造循环和其他看起来不可能的智能的东东，山一样多得运行配置文件。我需要监视mongrel， 所以我简单的循环他们的端口号，消除重复，使得我得生活更容易。

```ruby
God.watch do |w|
  w.name = "gravatar2-mongrel-#{port}"
  w.start = "mongrel_rails start -c #{RAILS_ROOT} -P #{port}  \
         -P #{RAILS_ROOT}/log/mongrel.#{port}.pid -d"
  w.stop = "morgrel_rails stop -P #{RAILS_ROOT}/log/mongrel.#{port}.pid"
  w.restart = "mongrel_rails restart -P #{RAILS_ROOT}/log/mongrel.#{port}.pid"

  w.pid_file = File.join(RAILS_ROOT, "log/mongrel.#{port}.pid")

  ...
end

```

watch代表一个简单的进程，包括start, stop, 和/或restart操作。只要你喜欢，你就可以定义watche。 在上例中，我已经有一些rails实例运行在mongrel中，我需要mongrel一直运行。每个watch有一个独特的名字以便在后面的操作中识别。start和stop属性用来指定开始和结束进程的命令。假如restart没有设置，restart用一个stop和一个start代表。可选的grace属性设置了重置普通监视操作以前，start/stop/restart命令等待的时间。假如你正监视的进程是一个后天进程（我的也是），你需要设置pid_file属性。

```ruby
  w.behavior(:clean_pid_file)
```

behavio允许你伴随着start/stop/restart执行额外的命令。在我们的例子里，假如进程死了，它会留下PID文件。假如下次重新启动这个程序，就会启动失败，提示PID文件已经存在。所以启动程序时，我们想先清除PID文件。内建的clean_pid_file会清除。

```ruby
  w.start_if do |start|
    start.condition(:process_running) do |c|
      c.interval = 5.seconds
      c.running = false
    end
  end
  
```

watch包含由可执行的动作组成的条件应该返回true。 我用start_if块开始，这个块包含一个简单的条件。通过用一个识别字符调用condition条件来描述condition，这个例子中是:process_running. 每个条件描述一个poll间隔，这个间隔将覆盖默认的间隔。这个例子中，我想要每个5秒钟检查一次进程，而不是像其他condition一样用30秒间隔。设置condition poll的间隔使得运行比不太严格的测试（如:memory_usage和:cpu_usage）更频繁得严格测试成为可能（如:process_running).

```ruby
  w.restart_if do |restart|
    restart.condition(:memory_usage) do |c|
      c.above = 150.megabytes
      c.times = [3, 5]
    end
    
    ...
  end
```

和start_if类似，restart_if命令组合condition，然后触发restart。memory_usage条件将会失败，假如指定的进程使用了太多的内存。最大允许的内存通过above属性来指定（你可以用kilobytes, megabytes, 或者gibabytes助手）。为了触发restart需要触发的次数通过times设置。这个可以是一个整数，也可以是一个数组。整数意味着它必须连续失败许多次而数组[x, y]意味着必须y次中失败x次。

```ruby
 w.restart_if do |restart|
   ...

   restart.condition(:cpu_usage) do |c|
     c.above = 50.percent
     c.times = 5
   end
 end
```

为了同时监视CPU用量，我使用了cpu_usage条件。当mongrel的cpu用量连续超过50%超过5次，它将会重启。

```ruby
  w.lifecycle do |on|
    on.condition(:flapping) do |c|
      c.to_state = [:start, :restart]
      c.times    = 5
      c.within   = 5.minute
      c.transition   = :unmonitored
      c.retry_in     = 10.minutes
      c.retry_times  = 5
      c.retry_within = 2.hours
    end
  end
```

在lifecycle部分中的condition只要进程被监视就一直活动（它们通过状态的改变活动）。
:flapping condition守护除了god快速开始和重启你的应用的这些边缘的状态的其他情况。比如服务器配置变化或者外部服务的不可用都可能造成我得进程不能启动。那样的话，god将会一直重试启动我的进程。:flapping condition提供了两个水平的放弃不稳定进程。假如我翻译以上的option代码，那就是：假如watch在5分钟里被启动或者重启了5次，然后不再监视它。。。然后10分钟后，再次监视他看看是否只是一个临时的问题；假如进程在两小时里依然不稳定，然后彻底放弃监视。

就这样！

### 开始和配置GOD

如果把god作为一个后台进程，只需要把配置文件的路径传递给god（你需要使用sudo假如你在linux使用event或者想要使用setuid/setgid）:

` $ sudo god -c /path/to/config.god `

当你写配置文件的时候，在前台运行god这样你能看见log消息，可能会很有帮助。你可以：

` $ sudo god -c /path/to/config.god -D `

你能启动、重启、停止、监测、不监测你的watch用同样的工具像这样：

` $sudo god stop gravatar2-mongrel-8200 `

###监测非后台进程

需要监视一个不是后台运行的脚本？没问题！ god会为你后台话并且跟踪这个进程。假如你没有指定一个pid_file,它会自动后台话并且将PID文件保存在/var/run/god.

```ruby
God.pid_file_directory = '/home/tom/pids'

#自动后台化和创建pid文件
God.watch do |w|
  w.name = 'mongrel'
  w.pid_file = File.join(RAILS_ROOT, "log/mongrel.pid")
  
  w.start = "mongrel_rails start -P #{RAILS_ROOT}/log/mongrel.pid -d"
  
  #...
 end
 
 #不自动后台化
 God.watch do |w|
   w.name = 'worker'
   #w.pid_file = is not set
   
   w.start = "rake resque:worker"
   
   #...
 end
```

假如你宁愿将PID文件保存在不同的位置，你可以在你的配置文件顶端设置：

`God.pid_file_directory = '/home/tom/pids' `

目录god必须可写

#群组化WATCH
watch能被分配到组。这些组可以通过命令行一起控制。

```ruby
God.watch do |w|
  ...
  w.group = 'mongrels'
  
  ...
end

```
  
以上配置文件允许你使用一条命令控制watch（和这个组里的其他watch）：

` $ sudo god stop mongrels `

### 重定向你进程的STDOUT和STDERR
默认的，STDOUT被定向至/dev/null. 为了进入这个输出，你可以重定向STDOUT流到文件或者命令。

为了重定向STDOUT到文件，设置log属性为文件路径。这个文件将以append模式写入，假如不存在则创建它。

```ruby
God.watch do |w|
  ...
  w.log = '/var/log/myprocess.log'
  ...
end
```

为了重定向STDOUT到命令，设置log_cmd属性

```ruby
God.watch do |w|
  ...
  w.log_cmd = '/usr/bin/logger'
  ...
end
```

默认地，STDERR会重定向到STDOUT。你也可以通过设置err_log和err_log_cmd重定向他到文件或命令。

### 改变进程的UID/GID
你也可以改变god运行start/stop/restart命令的用户或群组。这个可以通过设置uid和gid来改变：

```ruby
God.watch do |w|
  ...
  w.uid = 'tom'
  w.gid = 'devs'
  ...
end
```

仅仅对字符串有效，对lambda命令无效。

### 设置工作目录
在运行你的进程之前，God 默认会把工作目录设置到 /。（感谢@kayakjiang）
你可以改变这个通过设置dir：

```ruby
God.watch do |w|
  ...
  w.dir = '/var/www/myapp'
  ...
end
```

### 设置环境变量
你可以通过env属性设置任何数目的环境变量。

```ruby
God.watch do |w|
  ...
  w.env = { 'RAILS_ROOT' => "/var/www/myapp",
            'RAILS_ENV'  => "production" }
            
  ...
end
```

### 使用CHROOT改变文件系统的根目录
假如你像要你的进程运行在chroot，简单的在watch上使用chroot属性。指定的目录必须存在而且有/dev/null.

```ruby
  God.watch do |w|
    ...
    w.chroot = '/var/myroot'
    ...
  end
```

### Lambda命令
另外用字符串指定start/stop/restart命令（为了通过shell执行），你也可以指定一个lambda。

```ruby
God.watch do |w|
  ...
  w.start = lambda { ENV['APACHE'] ? `apachectl -k graceful ` : `lighttpd restart` }
  ...
end
```
### 自定义默认的停止运行lambda
假如你没有提供一个stop命令，God会首先试图通过发送SIGTERM来停止你的进程。然后为了进程退出等待10秒钟。假如还没有退出，它会发送一个SIGKILL信号。你可以自定义stop信号，和/或者通过在watch上设置stop_signal和stop_timeout属性。

```ruby
  God.watch do |w|
    ...
    w.stop_signal = 'QUIT'
    w.stop_timeout = 20.seconds
    ...
  end
```

### 加载其他配置文件
你可以随意的把god配置文件分成几个文件，这样会更容易组织文件。你也可以通过使用Ruby的load方法来加载其他的配置文件，或者使用God.load, 它运行你使用god风格的路径：

```ruby
#加载所有的god配置文件
God.load "/usr/local.conf/*.god"
```

God直到所有的配置文件加载完才会开始监测操作。

### 动态加载文件进入一个已经运行的god
God允许你加载或者重新加载配置文件进入一个已经运行的实例。当你准备这样做得时候，有几件事情需要考虑：

  * 已经存在的watch会被新配置文件里的同名的watch覆盖
  * 所有的路径必须是绝对路径或者god运行的相对路径
  
将配置文件加载至一个正在运行的god，运行以下命令：

` $ sudo god load path/to/config.god `

动态加载的配置文件可以包含任何一个普通的配置文件，然而，全局变量例如God.pid_file_directory块将可能会被忽视（会在日志里产生一个警告）。

### 为单个watch得到日志文件
从许多日志里筛选某个指定的watch的日志可能让人倍感狼狈。你可以通过以下命令得到一个实时的log：

` $ sudo god log local-3000 `

这条命令会展示local-3000的watch的log，每秒更新一次。

你也可以提供一个和你的watch想匹配的简写log命令。假如碰巧匹配几个watch，匹配缩写如下：

` $ sudo god log 13 `

### 通知

God有一个内建的可扩展的通知框架，这使得当条件被触发时发送通知变得很容易。每个通知的类型有一套必须配置的参数。这些参数通过Contact Defaults可能被设置成全局的，或者通过Contact实例设置成不同的。

Contact Defaults - 一些不可能每个Contact都会改变的参数。你应该通过默认的机制设置那些参数。

```ruby
God::Contacts::Email.defaults do  |d|
  d.from_email = 'god@example.com'
  d.from_name  = 'God'
  d.delivery_method = :sendmail
end

```

Contact实例 - 每个contact必须有个一独特的名字集合。你可以分配每个contact到一个组。

```ruby
God.contact(:email) do |c|
  c.name   = 'tom'
  c.group  = 'developers'
  c.to_email = 'tom@example.com'
end

God.contact(:email) do |c|
  c.name   =  'vanpelt'
  c.group  =  'developers'
  c.to_email  = 'vanpelt@example.com'
end

God.contact(:email) do |c|
  c.name = 'kevin'
  c.group = 'developers'
  c.to_email = 'kevin@example.com'
end

```

Condition Attachment - 当事件触发时发送一个指定的通知，简单的设置condition的notify属性为单独的contact。

```ruby
w.transition(:up, :start) do |on|
  on.condition(:process_exits) do |c|
    c.notify = 'tom'
  end
end
```

有两种方法具体说明应该发送通知。第一个，容易点得方法就是上面的这种。每个condition可以单独设一次notify属性，当条件满足时触发通知。这个值可以是contact的名字或者contact的组，或者由他们组成的数组。

```ruby
w.transition(:up, :start) do |on|
  on.condition(:process_exits) do |c|
    c.notify = {:contacts => ['tom', 'developers'], :priority =>1, :category=> 'product'}
  end
end
```

另一种方法允许你指定优先级（priority）和类别（category）。额外的属性可以是任意的整数或者字符串，然后传递到通知子系统。

上面的通知将会以一个和下面类似的邮件通知。

    From: God &lt;god@example.com&gt;
    To: tom &lt;tom@example.com&gt;
    Subject: [god] mongrel-8600 [trigger] process exited (ProcessExits)

    Message: mongrel-8600 [trigger] process exited (ProcessExits)
    Host: candymountain.example.com
    Priority: 1
    Category: product

### 可用的通知方式
（god支持Campfire, email, Jabber, Prowl, Scout, Twitter多种方式，而且易于扩展，这里只翻译了Email的方式，其他可以前往[官网](http://godrb.com/)自行查看。）

#### Email
发送通知到指定的email地址

```ruby
God::Contacts::Email.defaults do |d|
  ...
end

God.conteact(:email) do |c|
   ...
end
```

     to_email    - 字符串：发送至该email地址
     to_name     - 字符串：接收人的名字
     from_email  - 字符串：来自于
     from_name   - 字符串：来自于
     delivery_method - 符号：发送的方式, [ :smtp | :sendmail ]
                      默认是:smtp
    
     === SMTP 选项 ( 当 delivery_method = :smtp) ===
     server_host  - 字符串：SMTP服务器主机名（默认是：localhost ）
     server_port  - 整型：SMTP服务器端口（默认为25）
     server_auth  - 布尔型： （默认为false）
     
     === SMTP Auth 选项 (当 server_auth = true) ===
     server_domain -字符串：域名
     server_user   -字符串：用户名
     server_password -字符串：密码
    
    
     === Sendmail 选项 (当 delivery_method = :sendmail) ===
     sendmail_path  -字符串：sendmail的路径（默认是“/usr/sbin/sendmail”）
     sendmail_args  -字符串：sendmail的参数（默认是 “-i -t”）
    
至此，基本翻译完毕。
