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

4.-

Just before to start testing, there are another couple of things to have into account. First, I'm using devise for authentication and the user must be logged in before doing any action. Moreover, I'm using FactoryGirls. Last but not least, The user should be able to do CRUD over the event passing valid and invalid attributes. So having said that, the first step is to create the Factory for the user, sign in the user, and create the Factory for the event we are going to test, and this should be get done before to execute any test. Therefore, in the **spec/controllers/events_controller_spec.rb** file:

{% highlight ruby %}

require 'rails_helper'

describe EventsController do

	before do
      @user = FactoryGirl.create(:user)
      sign_in @user
      @event = FactoryGirl.create(:event, user: @user)
	end
end

{% endhighlight %}

5.- Let's start testing **index** adding the following code:

{% highlight ruby %}

describe 'GET #index' do

		it 'populates an array of events' do
		    get :index, { :user_id => @user  }
		    expect(assigns(:events)).to eq(@user.reload.events)
		end

		it 'renders the index view' do
			get :index
			expect(response).to be_success
			expect(response).to have_http_status(200)
		end

	end

{% endhighlight %}

6.- The next step is to test 'Get #new'

{% highlight ruby %}

describe 'GET #new' do

		it 'renders the :new view' do
			get :new
			expect(response).to be_success
			expect(response).to have_http_status(200)
		end

	end

{% endhighlight %}

7.- Get #Show

{% highlight ruby %}

describe 'GET #show' do

		it 'shows the requested @event' do
			get :show, id: @event
			expect(assigns(:event)).to eq(@event)
		end

		it 'renders the :show view' do
			get :show, { :id => @event  }
			expect(response).to be_success
			expect(response).to have_http_status(200)
		end

	end

{% endhighlight %}

8.- Get #Update

{% highlight ruby %}

describe 'GET #edit / update' do

		context "with valid attributes" do

			it 'renders the :edit view' do
				get :edit, { :id => @event  }
				expect(response).to be_success
				expect(response).to have_http_status(200)
			end

			it 'updates the event' do
				patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, name: "Anakin")
				@event.reload
				expect(@event.name).to eq("Anakin")

			end

			it 'sends an email when event is updated' do
	    		expect {patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, name: "Han Solo")
	    			}.to change {ActionMailer::Base.deliveries.count }.by(1)
		    end
		end

    context "with invalid attributes" do

			it 'does not update the event with invalid attributes' do
				patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, name: nil)
				expect(response).to render_template :edit
			end

      it 'does not send the update email' do
				expect {patch :update, id: @event.id, event: FactoryGirl.attributes_for(:event, description:nil)
					}.to change {ActionMailer::Base.deliveries.count}.by(0)
			end	
    end
end


{% endhighlight %}

9.- Event #Create

{% highlight ruby %}

  describe 'EVENT create' do

		context 'with valid attributes' do
			
			it 'creates a new event' do
				 expect{
			        post :create, event: FactoryGirl.attributes_for(:event)
			      }.to change(Event,:count).by(1)
			end
		end
  end
{% endhighlight %}



#ON CONSTRUCTION

#RESOURCES
	 http://stackoverflow.com/questions/28425547/failing-rspec-tests-of-controller-with-nested-resource-and-associated-factorygir

	 http://blog.thefrontiergroup.com.au/2011/03/reloading-factory-girl-factories-in-the-rails-3-console/

 	https://github.com/stympy/faker
