---
  layout: post
  title: "Advanced Rspec: Stub & Mock"
---
Advaced Rspec: Stub & Mock
<!--more-->
    
    Defination:
    
    Stub
      For replacging a method with code that returns a specified result.
    
    Mock
      A stub with an expectations that the method gets called
      
```ruby
  # app/models/zombe.rb
  class Zombie < ActiveRecord::Base
    has_one :weapon
    
    def decaptitate
      weapon.slice(self, :head)
      self.status = "dead again"
    end
  end
  
  # Stub
  # Zombie.new.weapon.stub(:slice)
  # 
  # Mock
  # zombie.weapon.should_receive(:slice)
  #
  # spec/models/zombie_spec.rb
  
  describe Zombie do
    let(:zombie) { Zombie.create }
    it 'calls weapon.slice' do
      zombie.weapon.should_receive(:slice)
      zombie.decaptitate
    end
    
    context '#decaptitate' do
      zombie.weapon.stub(:slice)
      zombie.decaptitate
      zombie.status.should == 'dead again'
    end
  end
```
####Mock with param

```ruby
  # app/models/zombie.rb
  
  class Zombie < ActiveRecord::Base
    def geolocate
      Zoogle.graveyard_locator(self.graveyard)
    end
  end
  
  # spec/models/zombie_spec.rb
  
  it 'calls Zoogle.graveyard_locator' do
    Zoogle.should_receive(:graveyard_locator).with(zombie.graveyard)
    zombie.geolocate
  end
  
```

####Mock with param and returns

```ruby
  # app/models/zombie.rb
  def geolocate
    loc = Zoogle.graveyard_locator(self.graveyard)
    "#{ loc[:latitude] }, #{ loc[:longitude] }"
  end
     
  # spec/models/zombie_spec.rb
  it 'calls Zoogle.graveyard_locator' do
    Zoogle.should_receive(:graveyard_locator).with(zombie.graveyard).and_return({latitude: 2, longitude: 3})
    zombie.geolocate.should == "2, 3" 
  end   
  
```  

####Stub object

```ruby
  # app/models/zombie.rb
  def geolocate_with_object
    loc = Zoogle.graveyard_locator(self.graveyard)
    "#{ loc.latitude }, #{ loc.longitude }"
  end
  
  # spec/models/zombie_spec.rb
  it 'returns properly formatted lat, long' do
    loc = sub(lantitude: 2, longitude: 3)
    Zoogle.stub(:graveyard_locator).returns(loc)
    zoobie.geolocator_with_object.should == '2, 3'
  end
 
 ```
 
 ####Stubs in the wild
 
 ```ruby
   # app/mailers/zombie_mailer.rb
   class ZombieMailer < ActionMailer::Base
     def welcome(zombie)
       mail(from: 'admin@example.com', to: zombie.email, subject: 'Welcome Zombie!')
     end
   end
   
   # spec/mailers/zombie_mailer_spec.rb
   describe ZombieMailer do
     context '#welcome' do
       # This is not necessory. Should be stub.
       let(:zombie) { Zombie.create(email: 'ash@zombie.com')
       
       # Could change to next line:
       # let(:zombie) { stub(email: 'ash@zombie.com') }
       
       subject { ZombieMailer.welcome(zombie) }
       
       its(:from) { should include('admin@example.com')
       its(:to) { should include(zombie.email) }
       its(:subject) { should == 'Welcome Zombie!' }
     end
   end
   
```

    
    Note: More stub options
    
    target.should_receive(:function).once
                                    .twice
                                    .exactly(3).times
                                    .at_least(2).times
                                    .at_most(3).times
                                    .any_number_of_times
                                    
    target.should_receive(:function).with(no_args())
                                    .with(any_args())
                                    .with("B", anything())
                                    .with(3, kind_of(NUmeric))
                                    .with(/zombie ash/)
                                    