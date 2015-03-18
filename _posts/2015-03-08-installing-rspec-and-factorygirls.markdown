---
layout: post
title:  "Testing in Rails Part 1: Installing Rspec and Factory Girls"
date:   2015-03-08 11:47:12
categories: rails
---

1.- Add *rspec-rails* to the *gem* file.

{% highlight ruby %}
 group :development, :test do
  gem 'rspec-rails'
end
{% endhighlight %}

2.- Install the gem by running on the terminal:
				
	bundle

3.- Initialize th *spec/* directory by running on the terminal:

	rails generate rspec:install

This command will generate the following configuration files:

	.rspec
	spec/spec_helper.rb
	spec/rails_helper.rb

4.- Add *factory_girl_rails* to the *gem* file in the *:development* and *:test* group.
{% highlight ruby %}
 group :development, :test do
  gem 'rspec-rails'
  gem 'factory_girl_rails'
end
{% endhighlight %}

5.- Also we need to add the following gems in the *:test* group

{% highlight ruby %}
 group :test do
  gem 'faker'
  gem 'capybara'
  gem 'guard-rspec'
  gem 'launchy'
end
{% endhighlight %}

6.- Install these gems by running on terminal:
				
	bundle


##RESOURCES

	https://github.com/rspec/rspec-rails

	http://everydayrails.com/2012/03/12/testing-series-intro.html

---
#UNDER CONSTRUCTION
