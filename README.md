## Dance Up: Ruby on Rails Member Subscription Site
Dance Up is a software as a service (SaaS) web application built with Ruby on Rails. Other technologies used are jQuery, Bootstrap, and SCSS. Users can create a free account with limited user information access or a paid account with full access. Additionally, users can upload avatars and post status updates in their profiles. Payments are captured through Stripe. Mailers are handled by Sendgrid.

## Project Links
- [Live application](https://rails-dance-up.herokuapp.com/)
- [Code repository](https://github.com/neillsom/dance-up) 

## Screenshots
Landing Page:
![Landing](https://s3-us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_01_landing.jpg "Landing Page")

About:
![About](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_02_about.jpg "About")

Contact:
![Contact](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_03_contact.jpg "Contact")
	
Signup:
![Signup](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_04_signup.jpg "Signup")

Signup Success:
![Signup Success](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_04_signup_success.jpg "Signup Success")

Create Profile:
![Create Profile](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_05_create_profile.jpg "Create Profile")

Community:
![Community](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_06_community.jpg "Community")

My Account:
![My Account](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_07_my_account.jpg "My Account")

My Profile:
![My Profile](https://s3.us-west-2.amazonaws.com/neillsomerville/dance-up/neill_somerville_portfolio_dance_up_08_my_profile.jpg "My Profile")

## Tech / Frameworks used
<b>Built with</b>
- Ruby
- Ruby on Rails
- Javascript 
- jQuery
- HTML
- CSS
- SCSS

## Code Examples
#### User Model
```ruby
class User < ActiveRecord::Base
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  belongs_to :plan
  has_one :profile
  attr_accessor :stripe_card_token
  def save_with_payment
    if valid?
      customer = Stripe::Customer.create(description: email, plan: plan_id, card: stripe_card_token)
      self.stripe_customer_token = customer.id
      save!
    end
  end
end
```

#### User Profile Creation Form Controller
```ruby
class ProfilesController < ApplicationController
  before_action :authenticate_user!
  before_action :only_current_user
  def new
    # form where a user can fill out their own profile.
    @user = User.find( params[:user_id] )
    @profile = Profile.new
  end
  def create 
    @user = User.find( params[:user_id] )
    @profile = @user.build_profile(profile_params)
    if @profile.save
      flash[:success] = "Profile Updated!"
      redirect_to user_path( params[:user_id] )
    else
      render action: :new
    end
  end
  def edit
    @user = User.find( params[:user_id] )
    @profile = @user.profile
  end
  def update
    @user = User.find( params[:user_id] )
    @profile = @user.profile
    if @profile.update_attributes(profile_params)
      flash[:success] = "Profile Updated!"
      redirect_to user_path( params[:user_id] )
    else
      render action: :edit
    end
  end
  private
    def profile_params
      params.require(:profile).permit(:first_name, :last_name, :job_title, :phone_number, :contact_email, :description, :avatar)
    end
    def only_current_user
      @user = User.find( params[:user_id] )
      redirect_to(root_url) unless @user == current_user
    end
end
```

## Installation
*The project was written in Ruby 2.2 and Rails 4.1.0*
To run this project locally:
- Clone the repository: `git clone https://github.com/neillsom/dance-up YOUR_PROJECT_NAME`
- Move into the project directory: `cd YOUR_PROJECT_NAME`
- Install gems / dependencies: `bundle install`
- `bundle exec rake db:create db:migrate`
- Create membership plans in database
`$ bundle exec rails c`
` > Plan.create(name: 'basic', price: 0)`
` > Plan.create(name: 'pro', price: 10)`
` > exit`

- Start the application: `bundle exec rails server`
- App is now running on local host port 3000 http://localhost:3000/

## License
MIT License
Copyright (c) 2018 Neill Somerville

#### http://neillsomerville.com