---
layout: ryan
title: "Parameter Validation with Grape"
description: ""
category: 
tags: []
---

<style>
img {
    display: block;
    margin: 0 auto;
}
</style>

# Parameter Validation with Grape

A pain point I constantly deal with  when building Rails APIs is handling query parameter validation. There is no built in way to do this and I have seen many different hand rolled solutions. Often some Rails apps will just not preform validations at this level. I have revisited the Grape gem recently, a gem I have not used in several years. I find Grapes solution to this problem extremely elegant. 

<img src="http://i.imgur.com/GxeD9S5.png" width="90%"/>

### Why are query parameter validations important?
- Provides better feedback to consumer of the api
- Ability to detect problems earlier in the execution path leading to faster response times
- Do not need to depend as heavily on model or database level validations
- Specifications may require any missing parameter is reported back to the user

### An Example

In this example I am showing a simple Grape endpoint which could be used for provisioning a new User.

~~~ruby
module V1
  class User < Grape::API
    desc 'Create a new User.'

    # Query Parameter Validations
    params do
      requires :age, type: Integer, desc: 'Age of User'
      requires :name, type: String, desc: 'Name of User'
    end

    post do
      # Authenticate the User
      authenticate!
      # Create a new user!
      User.create!({
        age: params[:age]
        name: params[:name]
      })
    end
  end
end
~~~


With the query parameter validations set up on Name and Age we validate the request first. In this case we simply are checking for the presence of both Name and Age. If we fail to specify Name we will get a 400 response status and a nice error message:

~~~javascript
{"error": "name is invalid"}
~~~

Just doing this simple query parameter validation in a typical Rails API would require much more effort. We would need to handle different response codes ourself and contract a custom validator object. Grape makes things so easy!

I am finding Grape APIs a joy to work with and this style of highly opinionated API designs is amazing for developer productivity. In the future I think Elixir's [Maru](https://github.com/falood/maru) I will be looking into. 

