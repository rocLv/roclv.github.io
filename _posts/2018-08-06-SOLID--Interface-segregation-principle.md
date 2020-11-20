---
  layout: post
  title:  "SOLID: Interface segregation principle"
  categories: SOLID
---

##### SOLID: 接口隔离原则

实例不应该强制依赖于它不使用的方法。

<!--more-->

接口隔离原则

   >
     实例不应该强制依赖于它不使用的方法。

换句话说

   >
     许多实例专用的接口比一般通用的接口更好。

所以我们创建的接口不应该包好我们不需要的方法。

让我们直接来看几个例子，因为这是解释这个原则最好的方法。

##### 反例

开始，有一个完全不同的返利。通过一些在 Reddit
上的讨论，我已经被说服了它不是最好的反例。所以我决定重新考虑，提供一个更好的例子。

旧的例子在在[这里](https://gist.github.com/marcinjak/1c138c9cd3ab23e90d2605fe13620e69)
