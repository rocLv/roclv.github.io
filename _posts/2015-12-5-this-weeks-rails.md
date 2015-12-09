---
layout: post
title: This week's Rails
---

这周Rails有什么新变化了呢？
<!--more-->

## 新东西

### [Rake用Rails替换了](https://github.com/rails/rails/pull/22457)

困扰新手的什么时候该用`rails`，什么时候该用`rake`，可能有很多新手都有点困扰。
现在你可以随心所欲的用`rails`完成所有Rails相关的操作了。

## 提高

### [解决了minitest-rails和Rails test
runner之间的命名冲突](https://github.com/rails/rails/pull/22456)

通过显示声明了Rails::TestUnitReporter全局命名空间解决了同时使用minitest-rails和
Rails test runner之间的命名冲突。

## 修复以下bug

* [初始化的STI（单表继承）模型现在会使用默认类型](https://github.com/rails/rails/pull/17169)
* 用`respond_to_missing?`替换了`ActionMailer::Base.respond_to?`
* 厘清连接池错误信息（以前就算用光连接词也不会有提示）

[This week in
Rails](https://rails-weekly.ongoodbits.com/2015/12/04/rails-command-infrastructure-and-more)
