In the last movie, we learned to create records using Active Record. In this movie, we're going to learn to update those records.

 Like creating records, there are two techniques that we can use for updating.

One, is where you **set the attributes one at a time, and then save**. And one, where you **set the attributes and save it all at once.**


#Find Save.

First, you find a record, then you set its values, and then you save it. It's very similar to what we had with new save.

#Find and update attributes.

Find the record, but then, we can set the values and save all in one step.

I'm going to use subject equals subject dot find one. This is the simplest way to find a record. We just use find, and we give it the ID that we want it to find.
```
2.2.3 :001 > subject = Subject.find(1)
  Subject Load (18.1ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 1 LIMIT 1
 => #<Subject id: 1, name: "First Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-24 15:25:18">
```
So that will find that subject for us. You can see that it issues an SQL command in order to find it. And it creates a new instance of the object for us to work with, populated with the correct values from the database. So, if we try subject dot new record,

```
2.2.3 :002 > subject.new_record?
 => false
```

we'll see that it's not a new record, and if we try subject dot name,
```
2.2.3 :003 > subject.name
 => "First Subject"
2.2.3 :004 >
```
we'll get back the name of the subject. Let's change the name now. Let's make it subject dot name equals initial subject.
```
2.2.3 :004 > subject.name = "Initial Subject"
 => "Initial Subject"
2.2.3 :005 > subject.name
 => "Initial Subject"
```

So now, if we try subject dot name, we get back initial subject. Let's try subject dot save, to save these changes.
```
2.2.3 :006 > subject.save
   (13.0ms)  BEGIN
  SQL (16.1ms)  UPDATE `subjects` SET `name` = 'Initial Subject', `updated_at` = '2017-03-27 07:40:21' WHERE `subjects`.`id` = 1
   (31.4ms)  COMMIT
 => true
```
Notice that this time, it knew to write an update statement. It knew that it was not a new record, that it already existed in the database, so instead of writing an insert statement like it did the last time we called save, in the previous movie. Instead, now it knew to write update, in order to update the data that was there. If we look at our object, you'll also see that the updated at time has changed as well.
```
2.2.3 :007 > subject.updated_at
 => Mon, 27 Mar 2017 07:40:21 UTC +00:00
2.2.3 :008 >
```

For contrast, you can see that the created app time is a different time.
```
2.2.3 :008 > subject.created_at
 => Fri, 24 Mar 2017 15:25:18 UTC +00:00
2.2.3 :009 >
```
Now, let's try the second technique. This is going to be to find a subject. And we're gonna find subject ID two.
 ```
 2.2.3 :009 > subject = Subject.find(2)
  Subject Load (2.5ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 2 LIMIT 1
 => #<Subject id: 2, name: "Second Subject", position: 2, visible: false, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-24 15:38:33">
2.2.3 :010 >
 ```
  And instead of being second subject, let's say subject dot update attributes, and then we'll provide a hash of the new attributes that we want to use. So, for example, we can change the name to be next subject.

And let's change visible, which was false, let's make it true.
```
2.2.3 :010 > subject.update_attributes(:name => 'Next Subject', :visible => true)
   (1.1ms)  BEGIN
  SQL (15.4ms)  UPDATE `subjects` SET `name` = 'Next Subject', `visible` = 1, `updated_at` = '2017-03-27 07:45:43' WHERE `subjects`.`id` = 2
   (3.9ms)  COMMIT
 => true
```
Notice that this time, it writes its update statement, and it returns true. Also notice that it doesn't include all of the values when it updates, it only includes the necessary values that need updating. Any attributes that haven't changed are left out of that update statement. There's no reason to send them to the database. Rails also knows which values have been changed. We've now seen the two main ways that you update records.
