---
title: Stop using resources, it's a crutch
date: "2019-09-11"
description: ""
---

```javascript
const helloWorld = "This is some code"
```

Rails has a lot of built-in functionality that makes it a great choice for fast-prototyping. One of the drawbacks is that there is a lot of "magic" going on. One of these magical methods is the use of resources in your routes.rb file. 

Let's say you have created a model and a controller for posts on a blog. 
```ruby
$ rails generate model Posts title: string, content: text
$ rails generate controller Posts
```

if you put
```ruby
resources :posts
```
in your routes.rb file. You will create the following routes:

get "/posts", to: "posts#index", as: “posts”
get "/posts/:id", to: "posts#show", as: “post”
get "/posts/new", to: "posts#new", as: “new_post”
post "/posts", to: "posts#create"
get "/posts/:id/edit", to: "posts#edit", as: “edit_post”
put "/posts/:id", to: "posts#update"
delete "/posts/:id", to: "posts#destroy"

These are all of the possible crud actions for any given model. 

you can always check what your routes you have available and what their prefixes are by typing
```ruby
$ rails routes
```
in the command line.

You can whip up all of the crud routes for your routes.rb file by simply putting resources :posts. However, this is a big crutch and you'll want to start creating your own routes as soon as you feel comfortable with setting your controller and views on your first couple projects.

While using resources is a nice way to quickly set up a blog application, you can't define your own routes, set your own prefixes, or gain the necessary understanding of how routes connect the URL request to the controller actions. Furthermore using a declarative approach for your routes makes code more readable and maintainable when working on a team.

>Using the as: keyword.

Another benefit of using a declarative approach is being able to set the prefixes for your routes. This allows you to set specific prefixes for routes.
