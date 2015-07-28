---
layout: post
title:  "Testing in Rails Part 3: Testing Controllers"
date:   2015-03-20 10:00:00
categories: rails, testing
---

1.- Config support/spec/devise

2.- Confic rails_helper
	Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }

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
