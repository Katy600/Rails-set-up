- [Kevin] In this movie, we'll use the Rails console to access our models and to create new records in the database. I'm going to show you two different techniques to accomplish this. The first I refer to as new/save. It's a three-step process.

First, we instantiate an object. Instantiate means to create a new object of the class. This object is also called an instance of the class. And then we can set values on that instance. And then when we have all the values set, we save the object to the database.

It's a three-step process: create a new object, set its values, and then save it. The second technique is to do it all in one step using Create. That is that we instantiate an object, set its values, and save it all in one step. Let's try both out starting with the new/save technique. From the root of your Rails project, you'll enter the Rails console with rails console. And then let's access our subject using subject = Subject, capital, .new.

```
katymccann (master *) simple_cms $ rails console
Running via Spring preloader in process 46973
Loading development environment (Rails 5.0.1)
2.2.3 :001 > subject = Subject.new
 => #<Subject id: nil, name: nil, position: nil, visible: false, created_at: nil, updated_at: nil>
2.2.3 :002 > subject.new_record?
 => true
2.2.3 :003 > subject.name = "First Subject"
 => "First Subject"
2.2.3 :004 > subject = Subject.new(:name => "First Subject", :position => 1, :visible => true)
 => #<Subject id: nil, name: "First Subject", position: 1, visible: true, created_at: nil, updated_at: nil>
2.2.3 :005 >
```


This is gonna create a new instance of my subject class.
```
2.2.3 :001 > subject = Subject.new
 => #<Subject id: nil, name: nil, position: nil, visible: false, created_at: nil, updated_at: nil>
2.2.3 :002 > subject.new_record?
 => true
2.2.3 :003 > subject.name = "First Subject"
 => "First Subject"
```
Now, Active Record has a number of methods built-in to it to make these objects smart. One of them is new_record?. It returns back and says, "True." It's a new record, it has not been saved to the database yet. I would now call subject.name and I'll set that equal to First Subject. And then I can read that value back. That value is now part of that attribute, but it's not saved to the database.

Now, this is a step by step way to do it. We also can do it slightly faster by using subject, I'm gonna overwrite the subject
```
2.2.3 :004 > subject = Subject.new(:name => "First Subject", :position => 1, :visible => true)
 => #<Subject id: nil, name: "First Subject", position: 1, visible: true, created_at: nil, updated_at: nil>
2.2.3 :005 >
```

that I've got now, Subject.new, and then provide a hash to new, name => "First Subject". And let's set position equal to 1. And visible is going to be true. So you can see that it went ahead and populated those values.

It does the exact same thing, this is just doing it step by step. But the same thing is true if I come back and I checked for new_record?, it says, "True."
```
2.2.3 :007 >
2.2.3 :008 >   subject.name
 => "First Subject"
2.2.3 :009 > subject.new_record?
 => true
```

Still a new record, I haven't saved it yet. Now let's try saving it,

```
2.2.3 :010 > subject.save
   (2.9ms)  BEGIN
  SQL (22.8ms)  INSERT INTO `subjects` (`name`, `position`, `visible`, `created_at`, `updated_at`) VALUES ('First Subject', 1, 1, '2017-03-24 15:25:18', '2017-03-24 15:25:18')
   (4.7ms)  COMMIT
 => true
```

 You can see that it actually created a MySQL insert statement, an INSERT INTO subjects, and we can see the values that it inserted. And it returns back to us true. It always return true or false when we call save. Now, if we check for subject.new_record with a question mark, you can see it comes back and says, "False."
```
2.2.3 :011 > subject.new_record?
 => false
2.2.3 :012 >
```
  It's not a new record anymore, it's saved in the database.

Let's also check some of the other values.

```
2.2.3 :012 > subject.id
 => 1
2.2.3 :013 > subject.created_at
 => Fri, 24 Mar 2017 15:25:18 UTC +00:00
2.2.3 :014 > subject.updated_at
 => Fri, 24 Mar 2017 15:25:18 UTC +00:00
2.2.3 :015 >
```

Subject.id, it assigned it the ID of 1 automatically, that's because it's my primary key and it auto increments. So it will always automatically assign that value for me. Let's try also subject.created_at. You can see that it gives us a created at date, and I also have one for updated_at. Those values were set automatically for me by Rails when I saved the object. This save pattern is really good for controllers because we can do things like if subject.save, and then puts "Saved!" Or we could do some other action, else, if the save failed for some reason, puts "Not saved!" And you can see that it returns back Saved! to me because it was saved successfully.
```
=> Fri, 24 Mar 2017 15:25:18 UTC +00:00
2.2.3 :015 > if subject.save
2.2.3 :016?>   puts "Saved!"
2.2.3 :017?>   else
2.2.3 :018 >     puts "Not saved!"
2.2.3 :019?>   end
  (1.0ms)  BEGIN
  (0.2ms)  COMMIT
Saved!
=> nil
2.2.3 :020 >
```
Now, the second technique that we're gonna use is the create technique. So I'm gonna create another subject here, I'm gonna overwrite the old one. But it's saved in the database now, so that's not a problem.
```
2.2.3 :020 > subject = Subject.create(:name => 'Second Subject', :position => 2)
   (0.3ms)  BEGIN
  SQL (6.9ms)  INSERT INTO `subjects` (`name`, `position`, `created_at`, `updated_at`) VALUES ('Second Subject', 2, '2017-03-24 15:38:33', '2017-03-24 15:38:33')
   (16.6ms)  COMMIT
 => #<Subject id: 2, name: "Second Subject", position: 2, visible: false, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-24 15:38:33">
2.2.3 :021 >
```

create, let's give this one a name of Second Subject, and position is gonna be 2, and then I'll hit return. Notice that right away, as soon as I created it, it created that Insert statement and it saved it. And what it returned to me was not true or false, it returned to me the object itself.

And that's useful because then I can assign it here. Before, I was able to do the assignment to a variable and then save it at my leisure, and I could worry about true or false. Here, I have to get back that object, it has to return it to me so that then I can continue to work with it. So now let's try subject.new_record?.
```
2.2.3 :021 > subject.new_record?
 => false
2.2.3 :022 >
```
It's not a new record, it's been saved. subject.id,
```
2.2.3 :022 > subject.id
 => 2
2.2.3 :023 >
```
 it has ID, too. And let's also notice that visible was set automatically to false.
```
2.2.3 :023 > subject.visible
 => false
2.2.3 :024 >
```

  That default setting kicked in, I didn't specify that as one of the attributes.

You don't have to provide all of the attributes, you can let Rails put in the defaults for it. As long as your database and your application don't object to it, it's not a problem. So now I've successfully created two records in my database, First Subject and Second Subject. In the next movie, we'll learn how to update records.
