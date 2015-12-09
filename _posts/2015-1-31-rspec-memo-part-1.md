---
layout: post
title: Rspec note part 1
---

Some code copied from [codeschool](http://codeschool.com)

**Rspec memo inspired by codeschool.com**
Here is part 1: Rspec base conception

<!--more-->
##基础
####安装
```ruby
  gem install rspec
```
####初始化
```ruby
  rspec --init
```
####Three pending ways

```ruby
  it "is an Ash"
  
  it "is an Ash" do
    pending
  end
  
  xit "is an Ash" do
  end
```
#### First Test

```ruby

  #spec/lib/zombie_spec.rb
  describe Zombie do
    it "has no brains" do
      zombie = Zombie.new
      expect(zombie.iq).to == 0
      expect(zombie.iq).to > 5 # or: expect(zombie.iq).to be > 5
      expect(zombie.iq).to
      expect(zombie.name).not_to eq('Ash')
      
      expect(zombie.rotting)to == false #or: expect(zombie.rotting).to be_false
      # any methods end with a '?' can use this be function    end
  end
  
 ```
 
 #### Equality and Identity sytax
 
     eq(exp)     #same value
     eql(exp)    #same value and type
     equal(exp)  #same object


#### True/False/nil

    be_true     # is true
    be_false    # is false
    be_nil      # is nil
    
#### Numeric comparisons

    be >= 10
    be_within(0.01).of(28.35) #floating point
    
#### match

    match /Ash \d/
    
####Array and string prefixies/suffixes

    start_with "Ash"
    start_with [1,2,3]
    end_with "end"
    end_with [4,5]
    
####Array matching

    match_array [a,b]
    match_array [b,a]
    # ordering is ignoring
    
####Ancestor Class

    
    be_a Zombie
    be_an <class>
    be_a_class_of <class>
    be_kind_of <class>
    be_an_instance_of <class>
    be_instance_of <class>
    
####Collection Size

    have(<n>).things
    have_at_least(<n>).things
    have_at_most(<n>).things
    
    # When the target is a collection, things can be anything
    # If the target owns a collection, "things" must be the name of the collection.
    


