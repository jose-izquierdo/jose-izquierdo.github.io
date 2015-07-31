---
layout: post
title:  "Testing in Rails Part 2: Testing Controllers"
date:   2015-03-20 10:00:00
categories: rails, testing
---

There are a couple of previous steps of configurations before to get your hands into the tests.

1.- Normally there is a problem popping up when reloading Factory Girl Factories in the Rails 3 Console. When you hit reload, the factories either stop working or will point out to the wrong class in Rails' console. This all happen due to devise. The problem comes up because devise uses a mapping between classes and routes, so when a factory built object comes through to Devise after a console reload, or a class redefinition then it will fail. This is a usual problem in development and test environments.

Therefore, in order to reload Factories to have everything working you have to put the following chunk of code in **config/application.rb**

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


2.- Now, add the following line to **spec/rails_helper.rb** file.

{% highlight ruby %}

Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }

{% endhighlight %}

3.-

{% highlight ruby %}

describe 'GET #index' do

	before do
      @admin = FactoryGirl.create(:admin)
      @admin.confirm!
      sign_in @admin
    end
 end

{% endhighlight %}

#ON CONSTRUCTION

#RESOURCES
	 http://stackoverflow.com/questions/28425547/failing-rspec-tests-of-controller-with-nested-resource-and-associated-factorygir

	 http://blog.thefrontiergroup.com.au/2011/03/reloading-factory-girl-factories-in-the-rails-3-console/

 	https://github.com/stympy/faker
