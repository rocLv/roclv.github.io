---
layout: post
title: speed up rails test 10x with PORO domain
---

本文介绍了使用PORO域而不是持久域来提高rails的测试性能。
（这里诞生了一个问题：为了测试写代码，这样对吗？我觉得不对，不过知道有这样的方法也不错。）
想了解的可以进来看看。

<!--more-->

## Trick 1: 在测试文件里，直接引用要测试的文件：

```ruby
# spec/models/some_model_spec.rb
# here is the trick
require File.join(ROOT, 'app','models','some_model')

# ! not 
# require 'spec_helper'
# test

```

## Trick 2: 使用Perpetual Gem，采用DataMapper方式，而不是ActiveModel
```ruby
# replace
# all = SomeModel.all
# to
all = Perpetual[SomeModel].all
```

不过，我并不觉得这样就好了。
而且Perpetual目前只支持Postgresql和MongoDB

欢迎大家留言讨论。


