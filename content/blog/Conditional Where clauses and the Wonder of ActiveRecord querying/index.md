---
title: Using Conditional Clauses in ActiveRecord
date: "2019-12-30"
---

## My problem

I needed to conditionally add a where clause to an Active Record Query only when the user defined a tag value. If no tag was inputted, then the published posts should still be queried with out the tag filter.

I wrote something like this:
```ruby{numberLines: false}
  def index
    if @tag.nil?
      Post.where(published: 'true')
      ...# additional querying clauses
    else
      Post.where(published: 'true')
      ...# additional querying clauses
        .where(tag: @tag)
    end
  end
```
I knew that if I had a value for tag I wanted one query to run, and if I didn't have a value for tag I wanted another query to run. 

This code just looks bad, but I didn't know a pattern that could achieve this same thing. 

I just wanted one query to run for index, and I didn't want the wrong one to run depending on the params inputted by the user. 

Luckily ActiveRecord doesn't query 

## An Elegant Solution

By saving the base query in a variable, I create an ActiveRecord::Relation object, which can be modified with additional chaining in the next line.

```ruby{numberLines: false}
  def index
    query = Post.where(published: 'true')
            ...# additional querying clauses
    query.where(tag: @tag) if @tag.present?
    query
  end
```

There is obviously less duplication, and I simply chain on the additional where clause if the instance variable is present. 

## So how does Active Record Work then?

When you create a query with ActiveRecord you are not necessarily hitting the database, you are building a set of instructions called an ActiveRecord::Relation, which will eventually hit the database when it is executed with a "kicker"

An ActiveRecord::Relation is an object that has directions on how to query the database. These directions can be modified up until they are executed. 

A kicker is a method that executes an ActiveRecord Relation on the database to fetch the data you are looking for. Your query statement will remain an ActiveRecord Relation until it is called with one of these methods. The reason why this isn't intuitive to new RoR users is that when you try out a query in your console, the console automatically calls .inspect by default, which is a kicker. Other kickers include to_a, and .each. 

Here's something you can try to see what I'm talking about. Pull up a rails project and open the console. Type in a simple query, with the .class method. 

```ruby

  >> User.all.class

```

depending on what version of rails you are running, you should get something like:

```ruby

  => User::ActiveRecord_Relation

```

However, if you add inspect, .to_a, or .each to the query, and add the .class method, 

```ruby

  >> User.all.to_a.class

```

you will get:

```ruby

  => Array

```
Which is a format that you can access for your view. Using inspect here explicitly would have resulted in a string output.

## Conclusion

Building an ActiveRecord relation by chaining different clauses together is an exampel of a chainable interface. This is a pattern that is used quite often, especially in ORMs. 

Additional clauses can be chained on to the existing query to refine the search results without sending another query to the database. In the example above we saw how this created a flexible data structure that can be continually manipulated with additional chainable methods until it needs to be called with a kicker. 

### references:
- [ActiveRecord Relations and Querying](https://stackoverflow.com/questions/10747106/how-does-rails-activerecord-chain-where-clauses-without-multiple-queries/10747692#10747692)
- [Chainable interfaces](http://www.railstips.org/blog/archives/2010/10/24/the-chain-gang/)
- [ActiveRecord queries RailsCast](http://railscasts.com/episodes/202-active-record-queries-in-rails-3)