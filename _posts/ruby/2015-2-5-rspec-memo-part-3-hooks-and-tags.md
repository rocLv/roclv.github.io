---
  layout: post
  title: Rspec about HOOKS & TAGS
---

This post is about HOOKS & TAGS

<!--more-->

#### BEFORE

```ruby
   describe Zombie do
        let(:zombie) { Zombie.new }
        before { zombie.hungry! }
      
        it 'is hungry' do
            zombie.should be_hungry
        end

        it 'craves brains' do
            zombie.should be_craving_brains
        end
     end
```

    
    NOTE:
      * before(:each) (default) --- run before each example
      * before(:all) run once before all
    also:
      *after(:each) -- run after each example
      *after(:all) -- run after all


#### SHARED EXAMPLES

```ruby
   # spec/models/zombie_spec.rb
   
   describe Zombie do
       it 'should not have a pulse' do
           zombie = Zombie.new
           zombie.pulse.should == false
       end
   end

    # spec/models/vampire_spec.rb

     describe Vampire do
         it 'should not have a pulse' do
             vampire = Vampire.new
             vampire.pulse.should == false
        end
      end

```

**Using shared_example**  
Declare a shared example group using **shared_examples**, and then include it in any group using **include_examples**, or use **it_behaves_like**.

Also, you could use **shared_context** and **include_context**.


```ruby
   # spec/models/zombie_spec.rb

    describe Zombie do
        it_behaves_like 'the undead'
    end

   # spec/models/vampire_spec.rb

     describe Vampire do
         it_behaves_like 'the undead'
      end

    # spec/support/shared_examples_for_undead.rb

     shared_exampls_for 'the undead' do
         it 'dose not have a pulse' do
              # using subject refers to the implicit subject
              subject.pulse.should == false
          end
       end
```

    
    NOTE:
      The syntax is using `it_behaves_like` key words in the normal examples;     
      using `shared_examples_for {}` in the shared files.
    
 
also, you can pass a paragram:

```ruby
     
    # spec/models/zombie_spec.rb

     describe Zombie do
         it_behaves_like 'the undead', Zombie.new
     end

     # spec/suppor/shared_examples_for_undead.rb

      shared_exampls_for 'the undead' do |undead|
           it 'does not have a pulse' do
               undead.pulse.should == false
           end
      end
```

#### Metadata and filters
未命名文件夹m未命名文件夹9未命名文件夹未命名文件夹y

```ruby
   describe 'when hungry' do
       context 'when hungry' do
           it 'wants brains'
             context 'with a veggie preference', focus: true do
                it 'still craves brains'
                it 'prefers vegan brains', vegan: true
            end
         end
    end

    # spec/spec_helper.rb
    Rspec.configure do |config|
       config.filter_run focus: true

       # run everything if none match
       config.run_all_with_everything_filter = true
       ...
    end
    
    $rspec --tags focus  # run with tags focus: true
    $rspec --tags ~slow  # run without tag slow
```    









  
    



 
   
