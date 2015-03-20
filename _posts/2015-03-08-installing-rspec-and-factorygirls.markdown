---
layout: post
title:  "Testing in Rails Part 1: Installing Rspec and Factory Girl"
date:   2015-03-08 11:47:12
categories: rails, testing
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

7.- Let's take a look on what we have installed here:

**factory_girl_rails** -> replaces the default fixtures of Rails for feeding test data.

**faker** -> generates emails, names, and other placeholders for the factories.

**capybara** -> provides an easy way to simulate users’ interactions with the application.

**guard-rspec** -> tests and runs specs automatically when it detects changes.

**launchy** -> It opens the web browser upon failed integration specs to show what the application is rendering.

8.- Do not forget to add a test database to the project. It should look like this:

{% highlight ruby %}
test:
  adapter: postgresql
  encoding: unicode
  database: db_test
  pool: 5
  username: 
  password:
{% endhighlight %}

Finally, to ensure there is a database, execute the following rake task:

	$ rake db:create:all

9.- Let's add **capybara** support by adding in the **spec/spec_helper.rb** file:let’s tell Rails to use it to generate starter files for our application’s test suite 

	require "capybara/rspec"

10.- Using the generators we can tell Rails to use it to generate starter files for our application’s test suite. Open config/application.rb and include the following code inside the Application class:

{% highlight ruby %}
config.generators do |g|
  g.test_framework :rspec,
    :fixtures => true,
    :view_specs => false,
    :helper_specs => false,
    :routing_specs => false,
    :controller_specs => true,
    :request_specs => true
  g.fixture_replacement :factory_girl, :dir => "spec/factories"
end 
{% endhighlight %}

Let's see what this snippet of code is doing:

**:fixtures** => true specifies to generate a fixture for each model (using a Factory Girl factory, instead of an actual fixture)

**:view_specs** => false says to skip generating view specs. I won’t cover them in this book; instead we’ll use request specs to test interface elements.

**:helper_specs** => false skips generating specs for the helper files Rails generates with each controller. As your comfort level with RSpec improves, consider changing this option to true and testing these files.

**:routing_specs** => false omits a spec file for your config/routes.rb file. If your application is simple, as the one in this book will be, you’re probably safe skipping these specs. As your application grows, however, and takes on more complex routing, it’s a good idea to incorporate routing specs.

And finally, **g.fixture_replacement :factory_girl** tells Rails to generate factories instead of fixtures, and to save them in the spec/factories directory.


##RESOURCES

	https://github.com/rspec/rspec-rails

	https://github.com/thoughtbot/factory_girl

	http://everydayrails.com/2012/03/12/testing-series-intro.html

---
#UNDER CONSTRUCTION
