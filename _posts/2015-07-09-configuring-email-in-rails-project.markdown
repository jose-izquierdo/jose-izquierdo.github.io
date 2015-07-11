---
layout: post
title:  "Configuring Email in Rails Project"
date:   2015-07-09 10:00:00
categories: rails, email, mailer
---

1.- Let's configure the email for the development enviroment. So, in the file **config/enviroments/development.rb** we will add how we want mail delivered: 

{% highlight ruby %}

config.action_mailer.delivery_method = :smtp

{% endhighlight %}

Also, you can use another two methods:
	
	- :sendmail -> Which is used when you want Action Mailer to attempt to deliever email just like :smtp

	- :test -> Which is great for unit and functional testing.  

2.- The following are the usual settings for Gmail: Add the following code (adapt it as you need) in the file **config/enviroments/development.rb**. 

{% highlight ruby %}

config.action_mailer.smtp_settings = {
    address: "smtp.gmail.com",
    port: 587,
    domain: "domain.of.sender.net",
    authentication: "plain",
    user_name: "wayne_industries",
    password: "password",
    enable_starttls_auto: true
  }

end

{% endhighlight %}

##How to Send Emails?

1.- Let's assume we have an app which creates events and we want to notify the user when a new event has been correctly created by email. We are going to use the **rails generate mailer** command to generate the mailer class, along with the names of the email action methods. So, from terminal inside the directory of our application:

> rails generate mailer UserNotifier new_event 

This will generate the mailer, its view, and its unit test. 

2.- Each method in the mailer class is responsible for setting up the environment for sending a particular email. Let's see how it works. In the file **app/mailers/user_notifier.rb we will add the following code:

{% highlight ruby %}
class UserMailer < ApplicationMailer

  default from: "info@wayne_industries.com"	
  def new_event(user, event)
    
    @user = user
    @event = event
    mail to: @user.email, subject: "Hey Congrats!! Your new event is been created!!"
  end
end

{% endhighlight %}

3.- The next step is to customize the view **app/views/user_notifier/new_event.html.erb**

{% highlight ruby %}

<div>Hi <%= @user.name %>!</div>

<div>
	<p>
	  Your event has been created. See details below:  
	</p>
	<p>
		Name: <%=@event.name%>
	</p>
	<p>
		Description: <%=@event.description%>
	</p>
	<p>
		Location: <%=@event.location%></span>
	</p>
</div>

{% endhighlight %}

4.- Once we have the template set up and the mailer method defined, we can use them in our regular controller to create/send emails **app/controllers/events_controller.rb

 {% highlight ruby %}

class EventsController < ApplicationController
  def create
    @event = current_user.events.create(permitted_params)
    if @event.save
	redirect_to @event
	UserMailer.new_event(current_user, @event).deliver
    else
	render 'new'
    end
  end
end

{% endhighlight %}


##How to Test Mailer Using Rspec?

1.- We have to configure **config/environments/test.rb** adding the following code:

{% highlight ruby %}

config.action_mailer.delivery_method = :test

{% endhighlight %}

2.- We're using UserMailer on the EventController, so we have to test that the email is sent when a new event is created. Please notice that I'm using **FactoryGirls**, **Fake**, and **Devise** for these tests. If you need further information visit [Installing Rspec and Factory Girl](http://jose-izquierdo.github.io/rails,/testing/2015/03/08/installing-rspec-and-factorygirls.html)

{% highlight ruby %}

describe EventsController do
	
  before do
    @user = FactoryGirl.create(:user)
    sign_in @user
    @event = FactoryGirl.create(:event, user: @user)
  end

  describe 'EVENT create' do
    context 'with valid attributes' do
      it 'sends an email when event is created' do
        expect {post :create, event: FactoryGirl.attributes_for(:event)}.to change {ActionMailer::Base.deliveries.count }.by(1)   		
      end
    end

{% endhighlight %}

 Event.name is validated as required in our model, so:

{% highlight ruby %}
    
    context "with invalid attributes" do
      expect {post :create, event: FactoryGirl.attributes_for(:event, name: nil)}.to change {ActionMailer::Base.deliveries.count }.by(0)   		
    end  
  end

end
{% endhighlight %}

3.- Let's provide another example of testing mailer funcionality for controllers. Let's assume that we have configured a mailer funcionanlity to send an email anytime the user make changes/updates to the event.

{% highlight ruby %}
describe 'GET #edit / update' do
  it 'sends an email when event is updated' do
    patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, name: "Han Solo")
    @event.reload
    expect {patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, name: "Han Solo")}.to change {ActionMailer::Base.deliveries.count }.by(1)   		
  end
end

{% endhighlight %}

4.- Last but not least, let's test **spec/mailers/user_mailer_spec.rb**

{% highlight ruby %}

describe UserMailer, type: :mailer do
  
  before do
      @user = FactoryGirl.create(:user)
      @event = FactoryGirl.create(:event, user: @user)
      @mail = UserMailer.new_event(@user, @event)
  end

  describe "new_event" do

    it "renders the subject" do
      expect(@mail.subject).to eq("Hey Congrats!! Your new event is been created!!")
    end

    it 'gets the receiver email' do
      expect(@mail.to).to eq([@user.email])
    end

    it 'renders the sender email' do
      expect(@mail.from).to eq(["info@coffeevent.com"])
    end

    it "renders the body" do
      expect(@mail.body.encoded).to match("Hi")
    end
  end

end

{% endhighlight %}





---






 