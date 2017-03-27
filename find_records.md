In your application, you're going to find that you're querying for existing records far more often than you're creating, updating, or deleting them. Active Record offers us many tools to make finding the right records easier. Over the course of the next few movies, we're going to learn how to construct queries in rails. We're going to begin with the simplest of these.
# Find Records

The first is the primary key finder.
```
Subject.find(2)
```

 We have the class name then a dot and then find and find is going to expect the primary key that we want to user to find the record.

So, in this case, it would find a subject who's primary key, on ID, is two. The result will either be an object, if it finds one, or it will raise an error if the object does not exist. You want to use this when it would actually be surprising to you, if the object isn't in the database. The second possibility is to use dynamic finders.

# Dynamic Finders

 Dynamic finders allow you to be able to find records by different attributes. They always start with find_by_ and then the attribute that you want to search on.
 ```
 Subject.find_by_id(2)
 Subject.find_by_name("First Subject")
 ```

So, it has to be an attribute that exist on that class, of course. Do, find_by_id or find_by_ name would both work, if we have attribute on subject for id and name. Find_by_id might seem very similar to what we were doing in the last movie, but there's an important difference, because find_by_id will return an object or it will return nil. You use it when you want to allow and handle the possibility that the record doesn't exist. It doesn't raise an error if it's not there.

It just returns nothing back to you, and then it's up to you to decide what you want to do with it. Another possibility is to just find all records, and we can do that with just Subject.all
```
Subject.all
```

and that will return an array of objects to us. It will get all of the subjects out of the database and it will instantiate them all into Ruby objects, put then in an array, and return the array to us. And, we can also find first and last by using Subject.first and Subject.last.

```
Subject.first
Subject.last
```
This will either return an object or it will return nil.

All of these find methods that we've seen so far make an immediate call to the database and return an object or an array of objects. That's different than the active relation queries which we're gonna see in the next few movies, which don't make a database call, until it's needed. Let's try a few queries in the Rails console. So, first, let's go into the Rails console, if you're not already there, and I'm gonna create a new subject. I'm gonna use create, and I'm gonna give it a name of Third Subject, and position will be three, so it will insert that record in the database.
```
2.2.3 :001 > Subject.create(:name => 'Third Subject')
   (1.7ms)  BEGIN
  SQL (13.2ms)  INSERT INTO `subjects` (`name`, `created_at`, `updated_at`) VALUES ('Third Subject', '2017-03-27 08:22:03', '2017-03-27 08:22:03')
   (7.4ms)  COMMIT
 => #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">
2.2.3 :002 >
```

Notice that even though this is the third subject I've created, and I gave it position of three, it's ID is number four. That's because, in the previous movie, I created a record that had ID three and then I deleted it. Auto increment will increment to the next value anyway, so the next value's gonna be four, three is left alone. So, that means, if I do subject = Subject.find(1), it returns the first subject to me.
```
2.2.3 :002 > Subject.find(1)
  Subject Load (7.2ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 1 LIMIT 1
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
2.2.3 :003 >
```

If I hit the up arrow and change that to a three, it returns no record to me, because that doesn't exist.
```
Subject Load (8.4ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 3 LIMIT 1
ActiveRecord::RecordNotFound: Couldn't find Subject with 'id'=3
```

What exists is four. So, I look for four, if I want to find that third subject.
```
from -e:1:in `<main>'2.2.3 :004 > Subject.find(4)
Subject Load (1.7ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 4 LIMIT 1
=> #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">
2.2.3 :005 >
```

 Now, let's try that one with three again, but instead of find, let's use find_by_id this time.
```
2.2.3 :005 > Subject.find_by_id(3)
  Subject Load (0.5ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 3 LIMIT 1
 => nil
```

This time, I don't get a nasty error back. It just looks for it in the database and if it doesn't exist, it returns nil to me. This assumes that I'm going to handle this in my application, that I'm going to take care of the fact that the subject didn't exist.

I don't want an error to show up. I don't want an error page to pop up for the user. I'm going to handle it, just by checking to see that there is no subject there. The constructed SQL is the same in both cases. The difference is how it handles the result. And then, let's try another one. Let's try subject = Subject.find_by_name and let's look for Initial Subject,
```
2.2.3 :006 > subject = Subject.find_by_name("Initial Subject")
  Subject Load (3.2ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`name` = 'Initial Subject' LIMIT 1
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
2.2.3 :007 >
```
and now it returns back that first subject to me, where the name was "Initial Subject". Let's find all subjects so this time I'll search for subjects,
```
subjects = Subject.all
```
 and I'll make it plural, I'll put an equal sign, and then we'll do Subject, singular because it's the class name, .all, so what I'm saying is tell the class Subject to get all of its records, and assign it to the variable subjects, plural.

```
2.2.3 :007 > subjects = Subject.all
  Subject Load (1.9ms)  SELECT `subjects`.* FROM `subjects`
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">, #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">]>
2.2.3 :008 >
```

Let's hit return. You can see that it went out and it requested all of those subjects. It instantiated them all as objects. You can see right here is one of the objects. And, you can see that it's returned inside an array. That's what those square brackets are. Let's see if we can iterate through those. We have subjects, plural, .each do |subject|, so each one of them will become the variable, subject, and let's output subject.name, end, and I'll hit return.

```
2.2.3 :008 > subjects.each do |subject|
2.2.3 :009 >     puts subject.name
2.2.3 :010?>   end
```


 So you can see that it output the names of those, Initial Subject, Next Subject, and Third Subject.

 ```
 Initial Subject
 Next Subject
 Third Subject
 ```

And then the return value, this is just in the console, the return value is the array itself.
```
=> [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">, #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">]
2.2.3 :011 >
```
So, the output is done by puts, but the return value that comes back is the full array. That's just the way that each works in Ruby. Let's try...
```
Subject.first
```

```
from -e:1:in `<main>'2.2.3 :012 > Subject.first
  Subject Load (2.4ms)  SELECT  `subjects`.* FROM `subjects` ORDER BY `subjects`.`id` ASC LIMIT 1
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
2.2.3 :013 >
```
You see that gives us back the first subject in the database. And ...
```
Subject.last
```

```
2.2.3 :013 > Subject.last
  Subject Load (0.5ms)  SELECT  `subjects`.* FROM `subjects` ORDER BY `subjects`.`id` DESC LIMIT 1
 => #<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">
2.2.3 :014 >
```
 And that will give us back the last subject in the database. Play with these a bit more, so that you get a feel for them. Then in the next movie, we'll talk about how you can perform database finds by constructing queries with conditions.
