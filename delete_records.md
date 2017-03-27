We've seen how to create and update records. Now, let's learn how to delete them. Deleting records is a two step process and we call it find and destroy. There's finding the record, just like we when we were updating the record, and then we destroy the record. Now here's a gotcha that you need to be careful of. There's also a method in Rails called delete and it may seem to work the same as destroy does. But delete bypasses some Rails features and might give you unexpected behavior. Destroy is the best method to stick with as a beginner.

If you're still in the Rails console, that's fine, there's no need to exit and go back in, but I'm going to start from scratch. And I'm going to first create a new subject. Let's do that using Subject.create and I'm going to give it a name and I'm going to call it, Bad subject.
```
2.2.3 :011 > Subject.create(:name => 'Bad Subject')
   (1.1ms)  BEGIN
  SQL (25.3ms)  INSERT INTO `subjects` (`name`, `created_at`, `updated_at`) VALUES ('Bad Subject', '2017-03-27 07:49:40', '2017-03-27 07:49:40')
   (2.2ms)  COMMIT
 => #<Subject id: 3, name: "Bad Subject", position: nil, visible: false, created_at: "2017-03-27 0
```
And that's going to save it to the database. I give it an ID of three so I can find that subject as my first step as subject, find, three. Just clear my screen so you can see it.

Here's the first step in the process, let's find the subject.
```
2.2.3 :012 > subject = Subject.find(3)
  Subject Load (1.9ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 3 LIMIT 1
 => #<Subject id: 3, name: "Bad Subject", position: nil, visible: false, created_at: "2017-03-27 07:49:40", updated_at: "2017-03-27 07:49:40">
2.2.3 :013 >
```
Now we want to get rid of this subject. We do that with destroy, subject.destroy.
```
2.2.3 :013 > subject.destroy
   (0.2ms)  BEGIN
  SQL (18.1ms)  DELETE FROM `subjects` WHERE `subjects`.`id` = 3
   (1.4ms)  COMMIT
 => #<Subject id: 3, name: "Bad Subject", position: nil, visible: false, created_at: "2017-03-27 07:49:40", updated_at: "2017-03-27 07:49:40">
2.2.3 :014 >
```
You can see that it issues a SQL delete statement. Delete from subjects where subjects ID is equal to three. And that's how it was able to delete it was by using its ID. And it returned back this object to me. Now even though the record is no longer in the database, I still have the object available for my inspection.

Right, I can still ask for subject.name.
```
2.2.3 :014 > subject
 => #<Subject id: 3, name: "Bad Subject", position: nil, visible: false, created_at: "2017-03-27 07:49:40", updated_at: "2017-03-27 07:49:40">
2.2.3 :015 > subject.name
 => "Bad Subject"
```
I still have it there, but it's no longer in the database and I can't change any values. Let's try subject.name going to be equal to test.
```
2.2.3 :016 > subject.name = "Test"
RuntimeError: Can't modify frozen hash
	from /Users/katymccann/.rvm/gems/ruby-2.2.3/gems/activerecord-5.0.1/lib/active_record/attribute_set/builder.rb:47:in `[]='
	from
```

Comes back and gives me an error. You see the error was can't modify a frozen hash. Right, so it freezes this object so it no longer can be modified, but I do still have it available for inspection. That's handy because then I have the ability to destroy something, but still then refer to it in my application.

For example, I could have a message appear that says, "Bad subject destroyed successfully." Right, I'd want to be able to have that name, bad subject available to me. But if we go back and we try to search for it again, subject = Subject.find(3).
```
from -e:1:in `<main>'2.2.3 :017 > subject = Subject.find(3)
  Subject Load (1.9ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 3 LIMIT 1
ActiveRecord::RecordNotFound: Couldn't find Subject with 'id'=3
```
 You can see that it is not in the database. I get another error, couldn't find subject with ID three. So we're successfully able to delete the record in the database.
