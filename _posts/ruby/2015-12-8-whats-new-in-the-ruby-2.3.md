---
layout: post
title: Ruby2.3添加的新特性
---

Ruby 2.3将在圣诞节发布，这里是Ruby2.3的一些新特性介绍。
<!--more-->

安装2.3预览版：
    
    #使用RVM
    rvm install 2.3.0-preview1
    #使用Rbenv安装
    brew upgrate ruby-build --HEAD
    rbenv install 2.3.0-preview1

##  安全导航操作符
2.3引入了一个新的操作符“&.”。有点像Rails的`#try`方法。在你要使用一个对象来调用某
个方法时，确认这个对象是否为nil的情况下很有用。这样避免了程序运行时抛出
NoMethodError异常。假如对像等于nil时，它会返回nil，否则调用该对象的方法。

```ruby
# ruby <= 2.2.x
if user && user.admin?
  # do something
end

# now, ruby 2.3
if user&.admin?
  # do something
end
```

## 不能修改的字符串
在Ruby里，字符串默认是可以修改的。现在使用不可修改的字符串，有助于提高程序性能，
减少内存消耗。不过在ruby 2.3该性能为可选的。计划是ruby
3.0开始，会成为默认的设置。

在ruby 2.3允许你通过在ruby文件前部声明` # frozen_string_literal: true`来启用该功能，

```ruby
# frozen_string_literal: true

str = 'cat'
str[0] = 'b'

# frozen.rb:5:in '[]=': can't modify frozen String (RuntimeError)
#   from frozen.rb:5:in '<main>'
```

也可以在运行时添加参数`--enable-frozen-string-literal`来测试你的app。

[具体介绍](https://bugs.ruby-lang.org/issues/11473)
## 添加Array#dig 和Hash#dig方法

```ruby
list = [
  [2, 3],
  [5, 7, 9],
  [ [11, 13], [17, 19] ]
]

list.dig(1, 2) # => 9
list.dig(2, 1, 0) # => 17
list.dig(0, 3) # => nil

dict = {
  a: { x: 23, y: 29 },
  b: { x: 31, z: 37 }
}

dict.dig(:a, :x) # => 23
dict.dig(:b, :z) # => 37
```

在处理JSON字符串的时候会非常有用。

## “Did you mean？”

当你得到NoMethodError的错误时，会提示相似的方法名，是不是很酷？妈妈再也不担心我记不住
方法名了。

```ruby
2.3.0-preview1 :026 > "foo bar".uppcase
NoMethodError: undefined method `uppcase' for "foo bar":String
Did you mean?  upcase
               upcase!
  from (irb):26
  from /Users/wangqsh/.rvm/rubies/ruby-2.3.0-preview1/bin/irb:11:in `<main>'
```

## Hash比较
现在可以用比较符来比较hash了。
```ruby
{ x: 1, y: 2 } >= { x: 1 } # => true
{ x: 1, y:2 } >= { x :2 } # => false
```

具体可以查看[hash
comparation](http://olivierlacan.com/posts/hash-comparison-in-ruby-2-3/)

## Hash也添加了#to_proc方法
```ruby
h = { foo: 1, bar: 2, baz: 3 }
p = h.to_proc
p.call(:foo) # => 1
p.call(:bar) # => 2

```

这里和哈希的方括号方法一样。主要用在使用`&`操作符的时候。

```ruby
h = { foo: 1, bar: 2 }

# ruby <= 2.2.x
[:foo, :bar].map { |key| h[key] } # => [1, 2]

# ruby 2.3
[:foo, :bar].map(&h)
```

## Hash#fetch_values

```ruby
h = { foo: 1, :bar, 2 }
h.fetch_values(:foo, :bar) # => [1, 2]

h.valus_at(:foo, :quux) # => [1, nil]
h.fetch_values(:foo, :quux) # => raise KeyError
```

## Enumerable#gerp_v
```ruby
list = %w(foo bar baz)
list.grep_v(/ba/) # => ['foo']
list.grep(/ba/ # => ['bar', 'baz']
```

## Numeric#positive? 和#negative?正式加入ruby

