# Authentication

## spree_auth_devise (Gem)
`spree_auth_devise` gem uses the authentication library
[Devise](https://github.com/plataformatec/devise/wiki) which provides functionalities to Spree
such as:
- Basic authentication
- Strong password encryption (with ability to specify your algorithms)
- "Remember me" cookies
- "Forgot my password" emails
- Token-based access (for REST API)

### Installation
* If it wasn't added automatically, add the following to your Gemfile
```ruby
gem 'spree_auth_devise', github: 'spree/spree_auth_devise'
```
* Install and run the migrations:
```bash
bundle && bundle exec rake spree_auth:install:migrations
bundle exec rake db:migrate
```
* In `config/initializers/spree.rb` change `Spree.user_class = 'Spree::LegacyUser'` to
`Spree.user_class = 'Spree::User'`
* To set up the admin user run:
```bash
bundle exec rake spree_auth:admin:create
```

### Default Configurations
* Passwords are stored in the database and encrypted with salt
* Authentications are done through database queries
* The following is enabled:
  * User Registration
  * Instant User Login (no validation emails)
  * Remember me
  * Password Recovery

Devise can be further configured extensively. See
[Devise wiki](https://github.com/plataformatec/devise/wiki) for more

### Account Confirmation Email
> Taken from spree_auth_devise README

To enable Devise's `confirmable` module that sends an email with a link to confirm an account:
* Add this to an initializer (typically `config/initializers/spree.rb`):
```ruby
Spree::Auth::Config[:confirmable] = true
```
* Add a Devise initializer (typically `config/initializers/devise.rb`):
```ruby
Devise.setup do |config|
  # Required so users don't lose their carts when they need to confirm
  config.allow_unconfirmed_access_for = 1.days
  # Fixes the bug where Confirmation errors result in a broken page.
  config.router_name = :spree
  # Add any other devise configurations here, as they will override the defaults provided by spree_auth_devise.
end
```

## *Customization Tips*
### Prerequisites
* A Spree application
* A model representing users(doesn't need to be called `User`) whether provided by gems such as
[Devise](https://github.com/plataformatec/devise) or
[Sorcery](https://github.com/NoamB/sorcery) or created manually

### Initial Setup
* If using model `User`, then in Spree's initializer *config/initializers/spree.rb* change this line:
```ruby
Spree.user_class = "Spree::User"
```
To:
```ruby
Spree.user_class = "User"
```
* Run Spree's custom user generator(tells Spree that you want to use `User` class as the class
that represents users):

```bash
bundle exec rails g spree:custom_user User
```
This creates:
1. A migration that adds Spree fields to your users table
2. An extension at `lib/spree/authentication_helpers.rb` to
  `Spree::Core::AuthenticationHelpers` module


* Run the new migration:
```bash
bundle exec rake db:migrate
```

### Authentication Routes Setup
`lib/spree/authentication_helpers.rb` methods need customizing so Spree finds your custom
authentication routes (Pay attention to code comments):
```ruby
module Spree
  module AuthenticationHelpers
     def self.included(receiver)
       receiver.send :helper_method, :spree_login_path
       receiver.send :helper_method, :spree_signup_path
       receiver.send :helper_method, :spree_logout_path
       receiver.send :helper_method, :spree_current_user
     end
     # Tell Spree who the current user of a request is
     def spree_current_user
       current_person
     end
     # Location of the login/sign in form in your application
     def spree_login_path
       main_app.login_path
     end
     # Location of the sign up form in your application
     def spree_signup_path
       main_app.signup_path
     end
     # Location of the logout feature of your application
     def spree_logout_path
       main_app.logout_path
     end
   end
end
Spree::BaseController.send      :include, Spree::AuthenticationHelpers
Spree::Api::BaseController.send :include, Spree::AuthenticationHelpers
ApplicationController.send      :include, Spree::AuthenticationHelpers
```
* URLs inside `spree_login_path`, `spree_signup_path` and `spree_logout_path` methods **must**
have `main_app` prefixed if the routes are inside the application
* Define `login_path`, `signup_path` and `logout_path` routes inside your `routes.rb`, e.g. if
you're using Devise:
```ruby
devise_scope :person do
  get '/login', :to => "devise/sessions#new"
  get '/signup', :to => "devise/registrations#new"
  delete '/logout', :to => "devise/sessions#destroy"
end
```
* You can customize `spree_login_path`, `spree_signup_path` and `spree_logout_path` methods
inside *lib/spree/authentication_helpers.rb* to use routing helper methods already provided by the authentication

> Any modification to files in *lib* while the server is running requires a restart

### `User` Model
Once you have specified `Spree.user_class` correctly, there will be
some new methods added to your `User` class:

* Methods added for `has_and_belongs_to_many` association, called `spree_roles`. This
association will retrieve all the roles that a user has for Spree
* Methods added for `spree_orders` association which returns all orders associated with the
user in Spree. There's also a `last_incomplete_spree_order` method which returns the last
incomplete spree order for the user. This is used internally to persist order data across a
user's login sessions
* Methods added for associations for address information for a user. When a user places an
order, the address information for that order will be linked to that user so that it is available
for subsequent orders
* `spree_api_key` getter and setter methods used for the API key that is used with Spree
* `generate_spree_api_key!` and `clear_spree_api_key` methods which generate and then clear the
Spree API key
* `has_spree_role?` method which can be used to check if a user has a specific role. This is used
internally to check if the user is authorized to perform specific actions, such as accessing the
admin section. Admin users of your system should be assigned the Spree admin role, like this:
```
user = User.find_by(email: "master@example.com")
user.spree_roles << Spree::Role.find_or_create_by(name: "admin")
```
To test this use `has_spree_role?` method; if the following returns *true* then the user has admin
permissions within Spree:
```
user.has_spree_role?("admin")
```

### Login link
To make the login link appear on Spree pages, you will need to use a Deface override.

* Create *app/overrides/auth_login_bar.rb* with this inside it:
```ruby
Deface::Override.new(:virtual_path => "spree/shared/_nav_bar",
  :name => "auth_shared_login_bar",
  :insert_before => "li#search-bar",
  :partial => "spree/shared/login_bar",
  :disabled => false,
  :original => 'eb3fa668cd98b6a1c75c36420ef1b238a1fc55ad')
```
* This overrides partial *spree/shared/login_bar* with a new one
*app/views/spree/shared/_login_bar.html.erb* (you can change the name) which contains:
```erb
<% if spree_current_user %>
  <li>
    <%= link_to Spree.t(:logout), spree_logout_path, :method => :delete %>
  </li>
<% else %>
  <li>
    <%= link_to Spree.t(:login), spree_login_path %>
  </li>
  <li>
    <%= link_to Spree.t(:signup), spree_signup_path %>
  </li>
<% end %>
```
* This will use URL helpers you defined in *lib/spree/authentication_helpers.rb* to
define three links that will be visible on all customer-facing pages:
    * Link to allow users to logout
    * Link to allow them to login
    * Link to allow them to signup

### Signup Promotion
Signup promotion event in Spree will not work if you're not using standard authentication.
To fix this set a session variable, in whatever controller deals with signup, after successful
signup code to trigger a notification event:
```
session[:spree_user_signup] = true
```
This informs Spree event notifiers of this event to apply a signup promotion to the current order.
