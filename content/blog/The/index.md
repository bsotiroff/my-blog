---
title: Active Record Queries - A practical guide
date: "2019-12-30"
---

# The Bad Code that got me inspired
This article starts with a really bad piece of code I wrote when I needed to conditionally add a where clause to an Active Record Query.

I wrote something like this:

```ruby{numberLines: true}
def index
  if @tag.nil?
    Post.where(published: 'true')
  else
    Post.where(published: 'true').where(tag: @tag)
  end
end
```

I only want the where clause associated with the tag if a tag value is present (e.g. passed through the controller through user input). This will allow me to add a tag such as `'ruby'`, or else just give me a query of all of the published posts, if I don't input a tag.

I knew this code was bad, but I didn't know a pattern that could achieve this same thing.

# The elegant solution

```ruby{numberLines: true}
def index
  query = Post.where(published: 'true')
  query.where(tag: @tag) if @tag.present?
  query
end
```

# So how does Active Record Work then?
There is obviously less duplication, and I simply chain on the additional where clause if the instance variable is present.

When you create a query with ActiveRecord you are not necessarily hitting the database, you are building a set of instructions called an ActiveRecord::Relation, which will eventually hit the database when it is executed with a "kicker"

An ActiveRecord::Relation is an object that has directions on how to query the database. These directions can be modified up until they are executed. 

A kicker is a method that executes an ActiveRecord Relation on the database to fetch the data you are looking for. Your query statement will remain an ActiveRecord Relation until it is called with one of these methods. The reason why this isn't intuitive to new RoR users is that when you try out a query in your console, the console automatically calls .inspect by default, which is a kicker. Other kickers include to_a, and .each. 


### references:
- [ActiveRecord Relations and Querying](https://stackoverflow.com/questions/10747106/how-does-rails-activerecord-chain-where-clauses-without-multiple-queries/10747692#10747692)
- [Builder Pattern](http://www.railstips.org/blog/archives/2010/10/24/the-chain-gang/)
- [ActiveRecord queries RailsCast](http://railscasts.com/episodes/202-active-record-queries-in-rails-3)