---
title: Policy design pattern in Rails
date: "2019-09-12"
---

Rails is incredibly flexible. If you think to yourself, "it would be great if it worked like this..." it probably works like that. Some other person probably has similar intuition and has created some functionality to fit that.

I recently learned how to create a simple query using a where clause in ActiveRecord. There are actually 5 different ways that will return the same result. 

For context, in this query we are searching for the employees that have a particular manager_id (5). Manager is a separate model that is accessed with a foreign key on the Employee model. 

I would normally use params[:id] to make the code dynamic, but here I have substituted with the integer 5. 

```ruby
Employee.where(:manager => 5)
Employee.where(:manager_id => 5)
Employee.where(:manager => Manager.first)
Employee.where("manager_id = ?", 5)
Employee.where("manager_id = :manager_id", :manager_id => 5)
```




