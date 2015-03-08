---
layout: post
title:  "Installing Paypal in Rails Project"
date:   2015-02-28 11:47:12
categories: ruby rails
---
1.- Create a developer account for Paypal Sandbox in 
	
	https://developer.paypal.com

<br>
2.- Add the gem to the Gemfile

	gem 'paypal-sdk-rest'

<br>
3.- From the console we execute 
		
	bundle install

<br>
4.- We configure the gem executing from the console the following command:

	rails g paypal:sdk:install

<br>
This command will generate  2 files:  
	
	1. "config/paypal.yml" -> It has to be configured with the data provided by the sandbox account
	
	{% highlight ruby %}
	development: &default
  		username: johnconnor-facilitator_api1.gmail.com
  		password: S6W9MEQFMNSUZN7H
  		signature: A7kN04CpKKzrZo9PJl0kSa1AQfAWAH2uG3SSRgG5bAZMPRikS-HCATvg
  		client_id: Aci7fqiVhoE6pwROdRHUluBihKjat-sPXUJ7sbdeNqsO4-txKiZCN1YgvZ8BXMk5THqMMUtwqYdkBch1 
  		client_secret: EF_TqIx5-zajOGa3gHrN3QO77OrXlkIL1Xsd9eoW3YhzmbQNMImB5Bypt5cVwBJITUnruNDLuLG7U19P

  		app_id: APP-80W284485P519543T
  		http_timeout: 30
  		mode: sandbox
  		sandbox_email_address: johnconnor-facilitator@gmail.com
  		# # with certificate
  		# cert_path: "config/cert_key.pem"
  		# # with token authentication
  		# token: ESTy2hio5WJQo1iixkH29I53RJxaS0Gvno1A6.YQXZgktxbY4I2Tdg
  		# token_secret: ZKPhUYuwJwYsfWdzorozWO2U9pI
  		# # with Proxy
  		# http_proxy: http://proxy-ipaddress:3129/
  		# # with device ip address
  		# device_ipaddress: "127.0.0.1"

	test:
  		<<: *default

	production:
  		mode: live
  		username: USERNAME
  		password: PASSWORD
  		signature: SIGNATURE
  		app_id: APP_ID
	{% endhighlight %}


<br>

	{% highlight ruby %}	
	2.- Initializer/paypal.rb
		PayPal::SDK.load("config/paypal.yml", Rails.env)
		PayPal::SDK.logger = Rails.logger
	{% endhighlight %}
<br>
5.- We generate de Payment in our Cart model

	{% highlight ruby %}
	def create_paypal_payment(order)
    	  @api = PayPal::SDK::AdaptivePayments.new
    	  @pay = @api.build_pay({:actionType => "PAY",
      	  :cancelUrl => "http://dev.johnconnor.com/cart",
      	  :currencyCode => "EUR",
      	  :ipnNotificationUrl => ok_paypal_cart_index_url,
      	  :receiverList => {:receiver => [{:amount => session[:order_total].to_f/100, :email => "johnconnor-facilitator@gmail.com"}] },
      # :returnUrl => "http://localhost:3000/cart/finished?order_number=" + order.order_number + "&uid=" + order.unique_guid
      	  :returnUrl => "http://dev.johnconnor.com/cart/finished?order_number=" + order.order_number + "&uid=" + order.unique_guid
      })
    	  @response = @api.pay(@pay)

      if @response.success? && @response.payment_exec_status != "ERROR"
        order.paypal_key = @response.payKey
      	order.save
      	redirect_to @api.payment_url(@response)  # Url to complete payment
   	  else
      	@response.error[0].message
      end
  end
	{% endhighlight %}
<br>
6.- Finally, we add the ok_payment method in which we will assign the right values like if valid_payment, delete_cart, and etc...

	{% highlight ruby %}
	def ok_paypal
      if PayPal::SDK::Core::API::IPN.valid?(request.raw_post)
        logger.info("IPN message: VERIFIED")
        order = Order.find_by_paypal_key params[:pay_key]      
        if order.present?
          order.valid_payment = true
          order.sentmail = true
          order.save

          order.order_details.each do |od|
            od.valid_payment = true
            od.save

            od.sales.each do |s|
              s.valid_payment = true
              s.save

              payment = number_with_precision((s.price * (1-(s.profit/100))) * s.quantity, precision: 2, separator: '.')
              amount = s.quantity
              event = s.picture.folder.event
              idorder = s.order_detail.order.id
              user = s.picture.folder.event.user

             AppMailer.send_sell_notification(amount, payment, event, idorder, user).deliver
           end
          end

          session.delete :cart
          session.delete :order_number
          session.delete :order_total

          @cart = nil
          @cart_sorted = nil

          AppMailer.send_order_confirmation_to_buyer(order).deliver
          AppMailer.send_order_notification(order).deliver
         end
         render nothing: true
      else

         logger.info("IPN message: INVALID")
          render nothing: true

     end
    else
      	logger.info("Conecction message: INVALID")
      	render nothing: true
     end
   end
  {% endhighlight %}


##RESOURCES


	https://github.com/paypal/PayPal-Ruby-SDK

	http://railscasts.com/episodes/141-paypal-basics

	https://developer.paypal.com/docs/

	
