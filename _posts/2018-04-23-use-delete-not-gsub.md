---
layout: post
title:  "Use `delete` rather than `gsub`"
date:   2018-04-27 16:16:01 -0600
---

`delete` is 2.x times faster than `gsub`

<!--more-->

```ruby

require 'benchmark/ips'
Benchmark.ips do |x|
  x.config(time: 5, warmup: 2)
  s = 'a\nb\nc\nd\n' * 2
  x.report("gsub") { s.gsub('\n', '') }
  x.report("delete") { s.delete('\n') }
  x.compare!
end

```

Here is the result:

    >
      Warming up --------------------------------------
                      gsub    45.000k i/100ms
                    delete   104.336k i/100ms
      Calculating -------------------------------------
                      gsub    516.847k (± 1.9%) i/s -      2.610M in   5.051625s
                    delete      1.433M (± 2.3%) i/s -      7.199M in   5.026350s
      Comparison:
              delete:  1433040.1 i/s
                gsub:   516847.4 i/s - 2.77x  slower



