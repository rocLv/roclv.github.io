---
  layout: post
  title: Rspec memo part 2 
---
 
DRY Rspec using **subject**

<!--more-->
    
 ###DRY Rspec    


####Refact with **subject**

```ruby
   describe Zombie do
       it 'responds to name' do
           zombie = Zombie.new
           zombie.should respond_to(:name)            
      end 
  end
```
after:

```ruby
   describe Zombie do
       it 'responds to name' do
          subject.should respond_to(:name)
       end
   end

   # subject = Zombie.new
   # only works using describe with a class
   
```

and:  **subject** can be omitted

```ruby
   describe Zombie do
       it 'responds to name' do
           should respond_to(:name)
       end
   end
```    
and:   Use   **it { }**
```ruby
   describe Zombie do
       it { should respond_to(:name) }
    end
```

####its

```ruby
   describe Zombie do
       it { subject.name.should == 'Ash' }
    end
```

Use **its**

```ruby
   describe Zombie do
       its(:name){ should == 'Ash' }
   end
```
one more thing:

```ruby
   describe Zombie do
      its('tweets.size') { should == 2 }
   end

   #  methods are used as string or symble
```
<br>
<br>
####Nest describe
```ruby
   describe Zombie do
      it 'craves brains when hungry'
      it 'with a veggie preference still craves brains when hungry'
      it 'with a veggie preference prefers vegan brains when hungry'
   end

# nest describe
  describe Zombie do
    it 'craves brains when hungry'

    describe 'with a veggie preference' do
        it 'stll craves brains when hungry'
        it 'prefers vegan brains when hungry'
   end
 end

 # nested again

    describe Zombie do
        context 'when hungry' do
            it 'craves brain'
            describe 'with a veggie preference' do
                it 'still craves brains'
                it 'prefers vegan brains'
           end
        end
    end

```
<br><br>

#### __*subject*__ in context

```ruby
    ...
    context 'with a veggie preference' do
        subject { Zombie.new(vegetarian: true) }

        it 'craves vegan brains' do
            craving.should == 'vegan brains'
        end
   end
   ...

```
#####Refact *subject*

```ruby
   context 'with a veggie preference' do
       subject { Zombie.new(vegetarian: true, weapons: [ axe ] }
       let(:axe) { Weapon.new(name: 'axe') }

       its(:weapons) { should include(axe) }
      
       its('swing(axe)') { should be_true }
   end
```     

Use **subject { variant }**

```ruby
   context 'with a veggie preference' do
       let(:zombie) { Zombie.new(vegetarial: true, weapons: [axe] }
       let(:axe) { Weapon.new(name: 'axe') }
       subject { zombie }

       ...

       it 'can use its axe' do
            zombie.swing*axe).should == true
       end
  end
```

Or : Use **subject(:zombie) { Zombie.new(vegetarian:true) } **

    
    Step by Step subject

     1. example starts to run
     2. needs to know subject
     3. zombie gets created

  **Note:** *let!* will let every time

####Refactor using subject

```ruby
    describe Zombie do
        it 'has no name' do
            @zombie = Zombie.create
            @zombie.name.should be_nil?
       end

       it 'craves brains' do
            @zombie = Zombie.create
            @zombie.should be_craving_brain
      end

      it 'should not be hungry after eating brains' do
         @zombie = Zombie.create
         @zombie.hungry.should be_true
         @zombie.eat(:brains)
         @zombie.hungry.shoul be_false
      end
   end

   #After refatoring

   describe Zombie do
      subject(:zombie) { Zombie.create }
 
      its(:name) { should be_nil? }

      it { should be_craving_brain }

      expect { zombie.eat(:brains).to change {
        zombie.hungry
      }.from(true).to(false)

   end

```
**Note:** 

    expect { event }.to change {
        some_attributes
    }.from(before_change).to(after_change)











