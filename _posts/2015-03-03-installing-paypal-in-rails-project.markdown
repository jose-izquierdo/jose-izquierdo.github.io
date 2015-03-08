---
layout: post
title:  "Installing Paypal in Rails Project"
date:   2015-02-28 11:47:12
categories: ruby rails
---
1.- Create a developer account for Paypal Sandbox in 
	
	https://developer.paypal.com

2.- Add the gem to the Gemfile

	gem 'paypal-sdk-rest'

3.- From the console we execute 
		
	bundle install
4.- We configure the gem executing from the console the following command:

	rails g paypal:sdk:install

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
