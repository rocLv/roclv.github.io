---
  layout: post
  title: "Advaced Rspec: Custom Matches"
---

Introducing to how to build custom matches

<!--more-->
####Refactoring

```ruby
  #app/models/zombie.rb
  
  class Zombie < ActiveRecord::Base
    validates :name, presence: true
  end
  
  # spec/models/zombie.rb
  describe Zombie do
    # We might do this a lot.
    it 'validates precence of name' do
      zombie = Zombie.new(nale = nil)
      zombie.valid?
      zombie.errors.should have_key(:name)
      # have_key like 'include', but for hashes
    end
  end
```

We might do this a lot. So we could build custom matches like:  
` zombie.should validate_presence_of_name`    
So, let's create:   

```ruby
  # spec/support/validate_presence_of_name.rb
  module ValidatePresenceOfName
    class Matcher
      def matches?(model)
        model.valid?
        model.errors.has_key(:name)
      end
   end
 end
  
```

For using this custom matcher, we should add another line:

```ruby
  Rspec.configure do |config|
    config.include ValidatePresenceOfName, type: :model
  end
```

If we add `type: :model`, this matcher will be available only to model specs.

```ruby
  # spec/support/validate_presence_of_name.rb
  module ValidatePresenceOfName
    class Matcher
      def matches?(model)
        model.valid?
        model.errors.has_key(:name)
      end
   end
   
   def validate_presence_of_name
     Matcher.new
   end
 end
  
```

Make more reuseful.

```ruby
  # spec/support/validate_presence_of_name.rb
  module ValidatePresenceOfName
    class Matcher
      def initialize(attribute)
        @attribute = attribute
      end
            
      def matches?(model)
        model.valid?
        model.errors.has_key(@attribute )
      end
   end
         
   def validate_presence_of(attribute)
     Matcher.new(attribute)
   end
 end
  
```

But what happens if the validation fails?

```ruby
  # spec/support/validate_presence_of_name.rb
  module ValidatePresenceOfName
    class Matcher
      def initialize(attribute)
        @attribute = attribute
      end
            
      def matches?(model)
        @model = model
        @model.valid?
        @model.errors.has_key(@attribute)
      end
      
      def failure_message
        "#{@model.class} failed to validate :#{@attribute} preseces"
      end
      
      def nagative_failure_message
        "#{@model.class} validated :#{@attibute} presence"
      end
   end
         
   def validate_presence_of(attribute)
     Matcher.new(attribute)
   end
 end
  
```

Chained methods

```ruby
  #spec/support/validate_presence_of.rb
  class Matcher
     def initialize(attribute)
         @attribute = attribute
         @message = "can't be blank"
     end

     def matches?(model)
         @model = model
         @model.valid?
         errors = @model.errors[@attribute]
         errors.any? { |error| error == @message }
     end

     def with_message(message)
         @message = message
         self
     end
  end

   # spec/models/zombie_spec.rb
   describe Zombie do
       it 'validates presence of name' do
           zombie = Zombie.new(name: nil)
           zombie.should validate_presence_of(:name).
                        with_message('been eaten')
           end
      end

    ....
```





   