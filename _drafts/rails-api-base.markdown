---
layout: post
title:  "Building a well documented Rails-API using Grape & Swagger"
date:   2016-07-19
comments: true
categories: rails
---
[comment]: <> (WELCOME & INTRODUCTION)

## Topic

This post is about [Ruby on Rails Version 5](http://www.rubyonrails.org) used as a simple [API](https://en.wikipedia.org/wiki/Application_programming_interface). Ruby and especially Rails solve some of the lower level problems you're facing when developing server-side applications. We'll be implementing [Swagger using grape-swagger-rails](https://github.com/ruby-grape/grape-swagger-rails) as API documentation and using [Grape](https://github.com/ruby-grape) as [DSL](https://en.wikipedia.org/wiki/Domain-specific_language).


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

For the sake of simplicity but for a working example of an API we're generating a model. Type the following inside of your rails project to generate a `Post`-model. We will use this model to do simple interactions with our database.

```shell
rails generate model Post title:string body:text
rails db:migrate
```

The first command generated a migration inside of `db/migrate/` and also a `app/models/Post.rb`. So we're good to go for our grape & swagger implementation. The second command used the generated migration to get the database on a state that we can work with.

### Integrating grape & grape-swagger-rails

1. add grape, grape-swagger and grape-swagger-rails to your Gemfile

```ruby
gem 'grape'
gem 'grape-swagger'
gem 'grape-swagger-rails'
```

2. `bundle`
3. add `mount GrapeSwaggerRails::Engine => '/api-docs'` to `./config/routes.rb`
4. if you want to hide your swagger api-docs in production you can use `unless Rails.env.production`
5. create `./config/initializers/swagger.rb` with the following contents:

```ruby
GrapeSwaggerRails.options.app_name = 'My Swagger API'
GrapeSwaggerRails.options.url      = '/api/v1'
GrapeSwaggerRails.options.app_url  = 'http://localhost:3000'
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

### Your half way through!

Starting Rails using `rails server` you can now open a browser on http://localhost:3000/swagger and see if everything is gone through alright.

### Creating the API endpoints

As we're starting of from scratch now we can be sure to be on Version 1 (or v1) of our API. So first we're going to create a folder under 

1. create `app/api/v1` folder
2. create `app/api/v1/api.rb` with the following content:

```ruby
require 'grape-swagger'

module V1
  class API < Grape::API
    version 'v1'
    format :json
    prefix :api

    before do
      header 'Access-Control-Allow-Origin', '*'
      header 'Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept'
    end

    mount SessionApi

    unless Rails.env.production?
      add_swagger_documentation(
        api_version: 'v1',
        mount_path: '/',
        hide_format: true,
        add_base_path: true
      )
    end
  end
end
```

3. create `app/api/v1/people_api.rb` with the following content:

```ruby
module V1
  class PeopleApi < Grape::API
    desc 'create a Person'
    
    params do
      requires :firstname, type: String, allow_blank: false, desc: 'Firstname of the user'
      requires :lastname, type: String, allow_blank: false, desc: 'Lastname of the user'
    end

    post '/people', http_codes: [
      [201, 'Create was successful'],
      [404, 'Create was unsuccessful']
    ] do
      
      person = Person.new(params);
      if person.save        
        status 201
        person
      else
        status 404
      end
    end

    desc 'A list of available people'

    get '/list' do
      Person.all
    end

  end
end
```

- add `mount V1::API => '/'`

### Conclusion

Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.


### Links & Resources

[http://www.ruby-grape.org/](http://www.ruby-grape.org/)
[http://swagger.io/](http://swagger.io/)