---
layout: post
title:  "Testing in Rails Part 2: Testing Models"
date:   2015-03-28 10:00:00
categories: rails, testing
---

1.- Since we're using **FactoryGirl**, let's create at first a factory for our model User. So, under spec/factories, let's create the file **users.rb**

{% highlight ruby %}
 require 'faker'

 FactoryGirl.define do
  factory :user do |f|
    f.name {Faker::Name.name}
    f.email {Faker::Internet.email}
    f.password  {Faker::Internet.password(8)}
    f.confirmed_at Faker::Time.between(2.days.ago, Time.now, :all)
  end
end
{% endhighlight %}

Let's stop here for a second to explain a few things about this chunk of code:

* This portion of code provides a factory that we can use throughout our specs.

* We're using **faker** to generate random data such as names, addresses, and phone numbers..
* I'm using *Devise* for authentication with the **confirmable** module activated. So, setting up **confirmed_at**  with *faker* will generate the factory with the user confirmed. 

* Sometimes, when we use the **reload!** command in the terminal the factories stop working, or point to a wrong class. This is a problem if you are using **devise** because **devise** uses a mapping between classes and routes, so when a factory built object comes through to Devise after a console reload, or a class redefinition then it will fail. This is commonly the case in development and test environments. However, adding the following code to the **application.rb** file will reload the factories properly to have it all work. 

{% highlight ruby %}
ActionDispatch::Callbacks.after do      
  # Reload the factories
  return unless (Rails.env.development? || Rails.env.test?)
  unless FactoryGirl.factories.blank? # first init will load factories, this should only run on subsequent reloads
    FactoryGirl.factories.clear
    FactoryGirl.find_definitions
  end
end    
{% endhighlight %}

2.- Let's create our model spec. Under **spec/models** we create the file **user_spec.rb** and add the following code:

{% highlight ruby %}
describe User do
  it "has a valid factory"
  it "is invalid without a name"
  it "is invalid without an email"
  it "is invalid wit a duplicate email"
  it "is invalid without a password"
end
{% endhighlight %}

3.- Let's write our first spec:

{% highlight ruby %}
describe User do
  it "has a valid factory"
    expect(FactoryGirl.build(:user)).to be_valid
  end	
  it "is invalid without a name"
  it "is invalid without an email"
  it "is invalid wit a duplicate email"
  it "is invalid without a password"
end
{% endhighlight %}

4.- In order to run the test we have to execute the followin command on the terminal:
		
	$ rspec spec/models/user_spec.rb

5.- Let's continue adding the rest of the code for our tests:

{% highlight ruby %}
describe User do
  it "has a valid factory"
    expect(FactoryGirl.build(:user)).to be_valid
  end	
  it "is invalid without a name"
    expect(FactoryGirl.build(:user, name: nil)).to be_invalid
  end
  it "is invalid without an email" do
    expect(FactoryGirl.build(:user, email: nil)).to be_invalid
  end
  it "is invalid wit a duplicate email" do
    FactoryGirl.create(:user, email: 'johnconnor@coffevent.com')
    expect(FactoryGirl.build(:user, email: 'johnconnor@coffevent.com')).to be_invalid
  end
  it "is invalid without a password" do
    expect(FactoryGirl.build(:user, password: nil)).to be_invalid
  end
end
{% endhighlight %}


----
## RESOURCES

http://blog.thefrontiergroup.com.au/2011/03/reloading-factory-girl-factories-in-the-rails-3-console/
 https://github.com/stympy/faker

----

#UNDER CONSTRUCTION
