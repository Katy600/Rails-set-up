 We've seen how to specify the conditions for our query, how to retrieve only the records that match a certain criteria, but there are three additional query methods that are helpful in allowing us to find exactly the records we want, and those are order, limit, and offset.

# Order
```
order(string)
```
Order is going to specify the sort order of the returned records. We can sort the records by any **column, ID, name, position, created at, etc**. We can sort them **alphabetically, or reverse alphabetically, ascending, or descending, chronologically, or reverse chronologically**.
# Limit
```
limit(integer)
```
Limit will **limit the results that are returned**. This becomes especially important when there are a lot of records which match our conditions. Let's imagine that we have a database of 20,000 customers. We wouldn't want to see a page with all 20,000 customers at one time, we'd probably want to view them broken up as pages, maybe just 20 at a time. In that case, we would limit the results to 20. If we then wanted to view page 51 of those results, we would still want to only get 20 records back, but we also want to skip over the first 1000 records that are on the previous 50 pages.

# Offset
```
offset(integer)
```
Offset **lets us skip over records before selecting the results to return**. *These query methods are all chainable*.
```
Subject.order(:position).limit(20).offset(40)
```

The combination of these three methods, along with our query conditions will help us to find the right records in the database.
# Order Argument
```
order(:position)
order("position")
order(:position => :asc)
order("position ASC")
order(:position => :desc)
order("position DESC")
```

Let's talk a bit more about the **argument to the order method**. There are a number of ways that you can specify the argument. In the example I just showed you, we were doing it with the symbol. **The symbol is a column on the database**, so we have ***order by position***. ***By default***, *that's going to order them in ascending order, that is, the number one comes first*, and then two, then three, then four, and so on.

We can also use a **string position**, and that works exactly the same. Because these both default to ascending, they do the exact same thing as the third and fourth lines. **We can specify that it should be ascending by using the hash notation, or by adding ASC to the string, so that its position space ASC**. This is actually an SQL fragment. If you've worked with SQL before, this is the way that you're used to specifying order by clauses, using position, and then ASC, or DESC, and that's what you see in the last two examples.

If we want to sort them in descending order, then we can either use the hash notation, and use the symbol DESC, or we can use an SQL fragment, and have a string that's position space DESC. In all of these examples, **if we wanted to sort by more than one column, then we would just put a coma, and then put the next column that we want to sort by after that**, and you can keep adding columns, it's unlimited how many columns you can sort on. This works great when we're working with a single table. When we start working with multiple tables that are joined together, then we start having to talk about SQL table disambiguation.

# Order SQL table disambiguation

That basically means ***making the table not ambiguous***. This is true of SQL, and **the way that SQL works, it's not a Rail specific thing.** When we work with **joined tables, we need to start disambiguating which table we're talking about**, *Especially if those joined tables have the same column names*. Let's say we've joined together our subjects and our pages table in one of our queries.
```
order('subjects.created_at ASC')
```
Well, then, we need to say, when we say sort by created at, We need to specify, are we saying sort by the created at on subjects, or sort by the created at that's on pages? **We need to add the table name in front of the attribute**, and we **need to use this SQL fragment notation**, that will keep SQL happy.

Most of the time, you will be able to get by by just simply using the column names. Let's try these in the console.


```
subjects = Subject.order(:position)
```

Now, you notice I'm not saying to do a find, but that's what it knows to do. It knows that this is a query method, and so it's going to find subject, and order it by position.

```
  Subject Load (5.1ms)  SELECT `subjects`.* FROM `subjects` ORDER BY `subjects`.`position` ASC
 => #<ActiveRecord::Relation [#<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">, #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```

There we go, *we get back an array of all of the subjects*, and sure enough, they are **sorted by their position**.


### Now, let's try it using the string notation.
```
subjects = Subject.order("position ASC")
```
We can have position ASC, and we get back the exact same thing.
```
Subject Load (0.3ms)  SELECT `subjects`.* FROM `subjects` ORDER BY position ASC
 => #<ActiveRecord::Relation [#<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">, #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```
### Let's try it now using position DESC,
```
subjects = Subject.order("position DESC")
```
and you will see that we get them back in reverse order. Here's position three at the beginning, and here's position one at the end.

```
  Subject Load (8.5ms)  SELECT `subjects`.* FROM `subjects` ORDER BY position DESC
 => #<ActiveRecord::Relation [#<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">, #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">]>
```

### Now, let's try limit.
```
subjects = Subject.limit(1)
```
and now we get back just one item.

```
  Subject Load (0.4ms)  SELECT  `subjects`.* FROM `subjects` LIMIT 1
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">]>
```

Now notice that **it still returned an array to me**. It's *not the same thing as saying first, we would still need to specify if we wanted that first one out of there, that would return the actual object*, but that's because *limit is really designed for returning multiple results*, let's say we wanted to limit to five, now it will return up to five records.

I only had three, so it just returned all three to me, but it was limited to five.

### Let's go back and try limit one, and this time, let's use offset with it.
```
subjects = Subject.limit(1).offset(1)
```
Now we get back subject ID two.
```
  Subject Load (1.6ms)  SELECT  `subjects`.* FROM `subjects` LIMIT 1 OFFSET 1
 => #<ActiveRecord::Relation [#<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```
### Let's try offset two,

```
subjects = Subject.limit(1).offset(2)
```

Now we get back subject ID four. It skipped over the first two, so it basically says return one, but skip over the first two when you're doing that.
```
Subject Load (6.5ms)  SELECT  `subjects`.* FROM `subjects` LIMIT 1 OFFSET 2
 => #<ActiveRecord::Relation [#<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">]>
```

### Last of all, let's try daisy-chaining all of these together.
Let's do subjects equals subject where visible is true, and order by position ascending, and limit it to one, and offset one.

```
subjects = Subject.where(:visible => true).order("position ASC").limit(1).offset(1)  
```
```
Subject Load (8.6ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1 ORDER BY position ASC LIMIT 1 OFFSET 1
=> #<ActiveRecord::Relation [#<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>

```
Now, the order of these daisy-chained elements doesn't generally matter. Active release and is going to take all of the things that we've asked for, and try to compile them into one query.

The only reason it would make a difference which order is if one of the later options was overriding a previous option. As long as they're not overriding each other and competing, then it doesn't matter which order they've been specified.
