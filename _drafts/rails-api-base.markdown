---
layout: post
title:  "Building a well documented Rails-API using Grape & Swagger"
date:   2016-07-19
comments: true
categories: rails
---
[comment]: <> (WELCOME & INTRODUCTION)

## Topic

This post is about [Ruby on Rails Version 5](http://www.rubyonrails.org) used as an [API](https://en.wikipedia.org/wiki/Application_programming_interface). Ruby and especially Rails solve some of the lower level problems you're facing when developing server-side applications. We'll be implementing [Swagger using grape-swagger-rails](https://github.com/ruby-grape/grape-swagger-rails) as API documentation and using [Grape](https://github.com/ruby-grape) as [DSL](https://en.wikipedia.org/wiki/Domain-specific_language).


[comment]: <> (CONTENT #1-n)

### Prerequisites

There are some things you should have beforehand:

- a running Ruby installation (at least ruby-2.2.0)
- `gem install rails` which basically installs the newest Rails into your Gemset

### Creating your Rails api

<!-- ![Rails](http://rubyonrails.org/images/rails-logo.svg){: .center-image } -->

When it comes to API's we're happy that the new Rails Version 5 [has some nice new announcements made](http://weblog.rubyonrails.org/2016/6/30/Rails-5-0-final/) including a so-called "API mode". It allowes you to create a slimmed down skeleton of a rails app, sparing all unnecessary frontend stuff.

So what are we going to do in this tutorial?

1. bootstrapping a simple Rails5-API
2. integrating swagger as API-documentation and endpoint-configuration
3. test our API-endpoints using rspec

We're starting with a basic Rails-API using the API-mode:

```shell
rails new api_base --api
```

Exploring the Application you will see some major differences to default Rails projects:

- The `app/views` folder the mailer template is the only thing left.
- The `ApplicationController` now inherits from [ActionController::API](http://api.rubyonrails.org/classes/ActionController/API.html) instead of [ActionController::Base](http://api.rubyonrails.org/classes/ActionController/Base.html).

### Creating a basic model to work with

For the sake of simplicity but for a working example of an API we're generating a model. Type the following inside of your rails project to generate a `Person`-model. We will use this model to do simple interactions with our database.

```shell
rails generate model Person firstname:string lastname:string
```

The command generated a migration inside of `db/migrate/` and also a `app/models/Person.rb`. So we're good to go for our grape & swagger implementation.

### Integrating grape & grape-swagger-rails

1. add `gem 'grape-swagger-rails'` to Gemfile
2. `bundle`
3. add `mount GrapeSwaggerRails::Engine => '/swagger'` to `./config/routes.rb`
4. create `./config/initializers/swagger.rb` with the following contents:

```ruby
GrapeSwaggerRails.options.app_name = 'My Swagger API'
GrapeSwaggerRails.options.url      = '/swagger_doc.json'
GrapeSwaggerRails.options.app_url  = 'http://swagger.yourhost.com'
```

### Enabling Swagger UI within the rails asset pipeline
	
Add sprockets to `config/application.rb`.

```ruby
require 'sprockets/railtie'
```

Include JavaScript in `app/assets/javascripts/application.js`.

```javascript
//
//= require_tree .
```

Include CSS stylesheets in `app/assets/stylesheets/application.css`.

```css
/*
*= require_tree .
*/
```

### Conclusion

Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
