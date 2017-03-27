- [Instructor] We've seen how to perform the basic find operations, but most times, on their own, these are too simple to search for records that match complex criteria. Instead, we'll need to construct queries. Let's start that by looking at conditions. **Conditions let you specify the criteria that the data must meet to be part of the query results**. So we **use the method where, and then tell it what conditions it ought to meet**. So for example,

```
 Subject.where(:visible => true).
```

This is going to look for **subjects where the visible column or attribute is equal to true.** We can express this condition in several different ways. We can use a string, we can use an array, or we can use a hash. Let's look at each one.

# String

With a string, you can just simply provide a string that gets dropped into that where statement.
```
"name='Test' AND visible=true"
```

This would **find all subjects where the name was Test and visible was equal to true**. This is **flexible because we get to issue raw SQL in here**, and **it will drop it straight into our query**.

However, it's not a great practice unless we're using static text only. **You want to be careful about SQL injection**. Now I'm not gonna go into depth about what SQL injection is here, but let me give you a quick idea. Let's suppose that instead of having the static string test that I was allowing a dynamic value to be inserted inside the string.
```
"name='#{@name}' AND visible=true"
```
Here I have the instance variable name that's being dropped into the string, so **the string is not static**. **It now has the ability to have data inserted into it.**

Now imagine that a **hacker** is able to take their code that they want and make that the value of name. When it gets dropped into my string, **now I'm running the hacker's code on my database**. That's a big problem, so you want to be very careful, and only use static strings as a condition. ***If we need something that's more dynamic***, then we're probably better off choosing **array**. Array **allows us to specify a static string, but you'll notice that now I have a placeholder after name**.
# Array
```
["name=? AND visible=true", "Test"]
```

name=? and then I have the *value that I want to drop into the string right where that question mark is as the next value in my array*. What happens is that ***rails takes that value and sanitizes it to make it safe to drop inside this string, so it's flexible just like our string was, but we get the benefit of having escaped SQL, which is safe from SQL injection***. So these are the two most flexible approaches because I can really put any SQL I want in there, but if we don't need that flexible of an approach, we may be better off just using the simplicity of a hash.

# Hash
{:name => 'Test', :visible => true}

And that's what you saw me use at the beginning. Here I've got a hash where **I'm looking for a subject whose name is test and visible is true**. It's simple, the SQL for it will be escaped. It happens behind the scenes when these get converted into a bit of SQL, so it is safe from SQL injection. **Each of the key-value pairs is going to be joined together with AND automatically**. We can use simple values like we have here, or we can also use ranges like 50 to 51, or we can provide an array 1, 3, 5, and 7, and it would look for exactly those values.

But it is simple, and so we don't have support for complex SQL ideas like OR, LIKE, less than, or greater than. It really is just looking for equality and ranges and seeing if those match.

##Another important point about conditions
is that they return an **ActiveRelation, which can then be chained together**. So for example,
```
User.where(:last_name => 'Smith')
where(:first_name => "John")
```
I can have User.where last name is Smith, and then I can have a dot, and right after that I can follow it with another where statement, where first name is John. The reason we can do this is because **the where clause does not execute the database call immediately**.

That's different than what we saw in the previous movie where we were finding records, and it was finding them right away. **The where clause waits until everything is done before it actually executes our clause**. So it gives us the **opportunity to chain on more query methods to the end of it**. And that allows us to construct the query that we want. Let's try these out.

```
 subjects = Subject.where(:visible => true)
```

It's gonna return to me all subjects where visible is true.
```
from -e:1:in `<main>'2.2.3 :002 > subjects = Subject.where(:visible => true)
  Subject Load (13.1ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```
You can see the SQL that it constructed. It tells you that right here. And you can see that I get back an array, an array of objects that match. Notice also it tells me that it's an active record relation. Now you may be saying, "Well wait a minute. "I thought you told us that we had the opportunity to chain "to chain these, that the query did not happen right away. "It sure seems like the query did happen right away here." Well, **in the console, it does happen right away because our command is done**. We haven't done anything else.

Had we been in regular RubyCode, or if we had been in a more complex bit of RubyCode here, it would have held off and waited. So **the reason that it finished is simply because I hit return**, and **it knew that that was the end**, so it went ahead and executed the command for us. Let's go ahead and just check and see

```
subjects.class
```
```
2.2.3 :003 > subjects.class
 => Subject::ActiveRecord_Relation
2.2.3 :004 >
```

to see that it's an **ActiveRecord_Relation object that it returned**, and you can also call

```
subjects.to_sql
```


**and that relation can tell you what the SQL is that it's going to execute**.
```
2.2.3 :004 > subjects.to_sql
 => "SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1"
2.2.3 :005 >
```
That can be handy for troubleshooting sometimes. Alright, let's try a few of the others.


```
subjects = Subject.where("visible = true")
```
Notice I'm **not using a hash format here. It's an actual string**. **It is static**, so it's okay to use it inside there, and you can see that it comes back and does the exact same thing, returns the same records to me.

```
2.2.3 :005 > subjects = Subject.where("visible = true")
  Subject Load (2.1ms)  SELECT `subjects`.* FROM `subjects` WHERE (visible = true)
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```

Let's try another version
```
subjects = Subject.where(["visible = ?", true])
```

That's going to be the **template that we're going to use for the query**. **The question mark is a placeholder**, and **now I need to tell it what value should drop into that placeholder**. And I'll use **true**. I'll hit return, and I get back the same results.

```
2.2.3 :006 > subjects = Subject.where(["visible = ?", true])
  Subject Load (3.6ms)  SELECT `subjects`.* FROM `subjects` WHERE (visible = 1)
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```
Now let me just show you that these are chainable.

```
subjects = Subject.where(:position => 1)
```
```
2.2.3 :009 >   subjects = Subject.where(:position => 1)
  Subject Load (2.2ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`position` = 1
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">]>
```

 Let's try
```
subjects = Subject.where(:position => 1).where(:visible => true)
```

```
2.2.3 :010 > subjects = Subject.where(:position => 1).where(:visible => true)
  Subject Load (0.3ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`position` = 1 AND `subjects`.`visible` = 1
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">]>
```

and let's try another version. We don't actually have to put these as separate statements.

```
subjects = Subject.where(:position => 1, :visible => true)
```

We can just make them where(:position => 1, and visible is true. So you can see I can do it either way. **I can make a separate where statement if I want, or I can just continue the hash**. Either way works.

Now let's try another one where we say where position is 1 and visible is false, and you'll notice that I get back an empty array in that case because nothing matched.

```
subjects = Subject.where(:position => 1, :visible => false)
```
```
  Subject Load (0.4ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`position` = 1 AND `subjects`.`visible` = 0
 => #<ActiveRecord::Relation []>
```
So you **either get back an array of objects or you get back an empty array, but you always get back an array when you search with where**.

If you want to get the first object that's in that array, then you have to add first at the end, and that will return the single object that's inside there. It's the same as the dynamic finders when we were using find by ID. So we're finding an array, and then we're telling it which one of those items out of the array we want to select. Next let's look at how we can use additional query methods to write more complex queries.
