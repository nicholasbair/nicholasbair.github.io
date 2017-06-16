---
layout: post
title:  "Wkend Project: Sinatra API Server, Part One"
date:   2017-06-16 23:01:50 +0000
---


After completing my Sinatra portfolio project [dogtracker](https://github.com/nicholasbair/dogtracker), I challenged myself to refactor the project into an API server.  To make things fun, I decided to build a simple (READ: not pretty!) mobile app to consume the API.  I have a little experience with React and Redux, so React-Native (hereafter RN) was an easy choice. Before I go any farther, please note this project is still WIP (work in progress).

![fitdog1](https://user-images.githubusercontent.com/8889403/27243575-94f40b3e-52b0-11e7-86af-516459e48dc2.gif)

The first step--and possibly the most fun--in refactoring my Sinatra project to meet the needs of [fitdog](https://github.com/nicholasbair/fitdog) was deleting the entire views directory.  Its common to namespace an API like so '/api/v1', to add namespacing in Sinatra, I added Sinatra Contrib (adds some supplemental features to Sinatra):

``` ruby
  # Gemfile
  ...
  gem 'activesupport', :require => 'active_support'

  # environment.rb
  ...
  require 'sinatra/namespace'
```

Now in the controllers, we can create the namespace block, and set the content type to JSON.  The namespace is prepended to the standard RESTful routes, for example: '/api/v1/signup'.

![namespacing](https://user-images.githubusercontent.com/8889403/27247301-37437802-52c5-11e7-9abe-f6a05154b19f.png)

**Logging In**

The nice thing about an API server, at least in this context, is the client is responsible for rendering the form, there are no GET routes in the user controller.  When the client sends a POST request to '/login', we parse the response using a helper as a convenience.

![parse-request](https://user-images.githubusercontent.com/8889403/27247305-47514c2e-52c5-11e7-8842-0275f552398e.png)

The form content comes from the client in the request body, so we convert it to JSON, and use a neat method #with_indifferent_access.  This allows us to access items cached in 'req' using dot notation.

![login](https://user-images.githubusercontent.com/8889403/27247309-5386bbfa-52c5-11e7-85a3-b640955fd9c6.png)

Once we have the request formatted, we can authenticate and generate a JWT token to send in the server response and be stored on the client.  We also add in some non-sensitive information about the user to push into application state.  The token is encoded prior to being sent.  The client then includes it in every subsequent request and the server decodes it, verifies that it is legitimate prior to allowing requests to protected routes.  JWT provides some nice functionality--most notable is scoping.  Basically the token is generated with an array a scopes the user is permitted to access.  In an effort to keep things simple, I am not taking advantage of that here.  Last, we convert the response to JSON for the client.

![jwt](https://user-images.githubusercontent.com/8889403/27247315-649a18a6-52c5-11e7-8b46-587cd84e83e8.png)

**GET Request**

When the client makes a GET request it sends the token in the request headers.  The request hits the before block and confirms the user is logged in.  Then, we pass the token to #current_user to decode, retrieve the requested information from the database, and convert to JSON.

![get-request](https://user-images.githubusercontent.com/8889403/27247326-76f004ca-52c5-11e7-90b3-e0bdccec9271.png)

**Thoughts**

Sinatra is drop dead simple and when paired with the power of ActiveRecord, creating a lightweight API server is quick and fun.  A blog about the client (RN) to follow.

Thanks for reading.

