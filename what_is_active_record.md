## Active record,
 when it's written all lowercase as two separate words refers to ***a commonly used design pattern for working with relational databases***.

It's not Rail specific. *You could use the active record pattern in any programming language*. It's an **approach to designing object oriented software**. ActiveRecord, when it's written as one word with a capital A and R, refers to the Rails implementation of the active record pattern.

Often you can use the terms interchangeably but it's helpful to understand the context and to know the difference. The ActiveRecord design pattern allows you to ***retrieve database data as objects***. And then to **work with them in an object oriented way**. Not just as static rows of data. If you've ever worked with database data as rows you know it can be cumbersome. Instead, ActiveRecord makes our objects intelligent. Our objects understand the structure of the database tables and knows how to interact with it. **Our objects contain not only data but they also contain code for creating, reading, updating and deleting rows in the database.**

Therefore, these objects can be manipulated and saved back to the database with just a few simple commands. Let me show you an example so you can get a feel for it.

```
user = User.new
user.first_name = "Kevin"
user.save # SQL INSERT

user.last_name = "Skoglund"
user.save # SQL UPDATE

user.delete # SQL DELETE
```


Here's some Ruby code where I'm interacting with a user object.

I start out by setting the variable user equal to a new instance of the user class. Which is going to be an **ActiveRecord class**. Next, I **set the attribute for first name equal to Kevin** then I **call user.save.** With that one simple command I've **now written an SQL INSERT** statement and I've **inserted the row in the database**.

It's much easier than writing raw SQL especially once our database objects become more complex. Next, I **set the attribute last name equal to Skoglund**. Then I call **user.save again**. This time, instead of doing an INSERT statement **Rails does an SQL UPDATE statement**. Because the **ActiveRecord object keeps track of the fact that this object has already been stored in the database**. So it not only constructs the SQL it makes smart choices, about the kind of SQL that it should write. And last, I call **user.delete**.

**Which writes an SQL DELETE statement which deletes the row from the database**. With just these few simple commands I can work with my database data in an object oriented way. It's easy to use and easy to understand. And I can let my objects handle writing all the SQL statements that's needed to get the job done. That's what I mean by intelligent objects. Next, let's talk about **ActiveRelation**. It's also sometimes referred to as **ARel** short for ActiveRelation. ActiveRelation, is an **object-oriented interpretation of relational algebra**.

The odds are very strong that you're not an expert in relational algebra. So let me describe it in a different way. **ActiveRelation simplifies the generation of complex database queries**. It allows us to **write small queries which can then be chained**, one after another. Like most Ruby objects can. **It takes care of handling complex joins and aggregations and makes sure that it uses efficient SQL to do it**. It also **manages the timing of when queries are executed**. Queries don't execute until we actually need them.

**ActiveRelation is going to be used heavily by ActiveRecord for queries, and for managing relationships between our objects.** You could think of ActiveRelation as sort of **the underpinning of ActiveRecord.** ActiveRecord sort of sits on top of it. **ActiveRelation mostly lives behind the scenes** but it's important to understand what it does. Let me give you an example to show you how simple it is to construct queries using ActiveRelation.
```
users = User.where(:first_name => "Kevin")
users = users.order("last_name ASC").limit(5)

SELECT users.* FROM users
WHERE users.first_name = 'Kevin'
ORDER BY user.last_name ASC
LIMIT 5
```

 Here, I'm *finding all users where the first name is equal to Kevin*.

In the **second line, I'm adding to that query**. And the query should also be **ordered by the last name and limited to just five results**. Notice that it breaks the query into discreet segments and allows us to daisy chain the segments together. At the end, ActiveRelation puts the pieces together and writes the SQL for the composite version. The example I show's probably not the exact SQL that it would write, but it gives you an idea of how it constructs SQL, from the segments. When we start doing complicated work joining lots of tables together, or aggregating data ActiveRelation will manage that complexity for us.
