
## Let's first consider when you might actually use a one-to-one association because there are two main use cases.
- The most common reason is for **unique items that a person or a thing can have only one of**. For example, an

```
employee has_one :office
```
```
student has_one :id_card.
```
 The ID card is unique. It belongs only to this one student and to no one else. And that student is only allowed to have one ID card. It's certainly possible to imagine a scenario where an employee might have two offices.

But by using a has_one relationship, we're rejecting that possibility in our design. We've made it a statement of fact that each employee can have only one. At the point that we want to allow employees to have more than one, we would need to revise our relationships to match. The second reason to use a one-to-one relationship is to break up a single table. For example,
```
customer has_one :billing_address
```
The columns associated with the billing address could go in the customer's table. We could also take those columns and move them into a separate table. Why would we do that? Sometimes it's useful to have all the billing addresses in one table where it's easy to access them.

If billing address is used rarely, while customer is used frequently, it might help your database performance. Or it may be that you want to remove complexity from one of your classes, and remove all of the columns and the code that's associated with it, into a separate model. For example, if we have a stage which has one lighting configuration,
```
Stage has_one :lighting_configuration
```

and the lighting configuration has 50 columns to it, and lots of code inside the model about how that lighting configuration functions, it might be beneficial to store it separately. In truth, you won't use one-to-one relationships very often.

It's far more common to use one-to-many relationships. In fact, in this simple CMS application we're gonna be building, there's no reason to use a one-to-one association. Rather than trying to invent something clunky, we're just going to try it out and then we're going to abandon it and move on. To get some experience with it, we're going to be be using subject and page. And just for the moment, we'll pretend that a subject can have only one page associated with it, and the page will belong to a subject.
```
Subject has_one :page
Page belongs_to :subject
```

There are two tips you should remember before we start defining associations. **The first is you want to make sure that any class with a belongs_to should be the side that has the foreign key**.
# 1.
 ***belongs_to equals foreign key***

We already defined our pages table with a foreign key of subject_id. So we're good on that point.

# 2.
 ***always define both sides of the relationship.***

You don't want to just say that the subject has one page and leave it at that. You also want to tell the page about it's relationship back to the subject, so that it goes both ways. Then if we have a subject object, we can find its page, or if we have a page object, we can find its subject. Let's begin by defining our relationship in our models.

## Let's go into our Subject model and you can put the relationships anywhere inside the class,
```
class Subject < ApplicationRecord

  has_one :page

  scope :visible, lambda { where(:visible => true) }
  scope :invisible, lambda { where(:visible => false) }
  scope :sorted, lambda { order("position ASC")}
  scope :newest_first, lambda { order("created_at DESC")}
  scope :search, lambda {|query| where(["name LIKE ?", "%#{query}%"])}
end
```
I like to put them at the top just so that they're right there at the beginning and I know what those relationships are. So for the subject, I'm gonna say that a subject has_one and then the symbol, page. That's going to tell ActiveRecord to make a relationship, to add the code necessary to have a relationship between the Subject class and the Page class. **Notice that page is singular, because I have one of them**. It makes sense that it would be a singular word.

## Now let's go into Page and let's define the other side of the relationship,
```
class Page < ApplicationRecord

  belongs_to :subject

end

```

belongs_to :subject. Again, singular. Now, we've made the relationship in both directions and Rails knows how to move between the two. We don't have to do anything additional as long as we've been following Rails conventions and sensible defaults. Remember we added our foreign key to the pages table and we gave it a name of subject_id. Because we followed that convention, Rails knows where to find the foreign key that it needs to make this relationship.

If we wanted to use something different, that's fine, we just have to add additional configuration so that Rails knows where to find it. For example, we could say :foreign_key and then let's say it's foo_id, right. Whatever it happens to be, that would tell it where it could find that foreign key. But we're using Rails conventions, so we don't need to do that. It's going to be built in. All right, let's go over to our console now, and make sure that you're in the root of your Rails application, launch the Rails console. And once that loads up, let's find our first subject.

```
subject = Subject.find(1)
```

I'll just find subject with ID of one.

```
  Subject Load (11.8ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 1 LIMIT 1
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
```

And let's ask that subject about its page,
```
subject.page
```
```
2.2.3 :002 > subject.page
  Page Load (25.5ms)  SELECT  `pages`.* FROM `pages` WHERE `pages`.`subject_id` = 1 LIMIT 1
 => nil
```

Notice that it goes to the SQL database. It looks in the pages table for a page where the subject ID is equal to one. It knew what the foreign key was and it used its current ID, right, the ID here, Subject id: 1, to look for a foreign key that matched in the database. And there was nothing that matched, so it returned back nil to us.

Notice also, that I was able to call a new method on my subject, which is page.
## When we define our has_one and belongs_to relationships, ActiveRecord automatically adds several methods for us that we can use.
```
subject.page
subject.page = page

page.subject
page.subject = subject
```

So **on our subject we can call page to find out what page it belongs to**. And we also have **page =, which allows us to assign a page to a subject.** And on the other side, we have page.subject and page.subject =. There are other methods as well, but these are the most important ones. Now let's create a page object, which we can assign to this subject.

```
2.2.3 :003 > first_page = Page.new(:name => "First Page", :permalink => "first", :position => 1)
 => #<Page id: nil, subject_id: nil, name: "First Page", permalink: 0, position: 1, visible: false, created_at: nil, updated_at: nil>
2.2.3 :004 >
```

I'm gonna set it equal to the variable first_page, I'll create a new instance of the Page class, and I'm gonna give it some attributes First Page and :permalink we'll make first, and :position gonna be 1. Okay, so now I have a new Page instance assigned to this variable. **Notice that it's not saved yet. I used new here, I didn't use create. So it has not been saved to the database. Notice that it has that foreign key of subject_id, and currently that's set to nil**.

It doesn't have a subject ID. You see that also if we do first_page.subject_id,
```
2.2.3 :004 > first_page.subject_id
 => nil
```
and we can call first_page.subject
```
first_page.subject
 => nil
```

and it will return the subject object if it's there. Now notice it did not have to make another database call, it did not go to the SQL database, it didn't need to. Because it knew that subject_id was equal to nil. So it knew there was no reason to look for the subject in the database. If it had had an ID, then it would have gone to the database to find information about that subject.
## Now let's make the assignment.

And let's say that subject.page = now we're gonna assign first_page. The instance of the page, we're gonna assign to it. Now remember this is not a saved object currently, right. We still haven't save first_page to the database. Subject has been saved to the database. But first_page is not.
```
2.2.3 :006 > subject.page = first_page
   (3.1ms)  BEGIN
  SQL (43.4ms)  INSERT INTO `pages` (`subject_id`, `name`, `permalink`, `position`, `created_at`, `updated_at`) VALUES (1, 'First Page', 0, 1, '2017-03-27 14:41:32', '2017-03-27 14:41:32')
   (48.0ms)  COMMIT
 => #<Page id: 1, subject_id: 1, name: "First Page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 14:41:32", updated_at: "2017-03-27 14:41:32">
```


Let's hit Return. And notice here that I get back a representation of my object, and it says here subject_id: 1. It has that foreign key there.

If we say first_page.subject_id it tell us that it's 1.
```
2.2.3 :009 > first_page.subject_id
 => 1
```

I say first_page.subject

```
:010 > first_page.subject
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
2.2.3 :011 >
```
it returns back the subject object to us, all right, that's what we're getting here is actually the subject object. Now **it didn't have to query the database because it had already loaded this subject into memory**. It already has it, it knows about it. I didn't have to go back to the database to find it again. Rails is smart that way about not accessing the database unless it needs to. Now notice also though, that some SQL got executed up here.

It inserted a new entry into our pages table, first_page was saved at the same time as we made the association.

```
2.2.3 :011 > first_page.new_record?
 => false
```
Comes back and says false, it's not a new record anymore. That's because **when we make the association, it gets the primary key from the subject, it sets it as the foreign key on pages, and then it saves the associated page's object to the database**. So it's very important when we make the association it does try to save the object.

So now that we've made the association, we can call
```
subject.page
```

it will give us back that first page from the other side as well.
```
 => #<Page id: 1, subject_id: 1, name: "First Page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 14:41:32", updated_at: "2017-03-27 14:41:32">
```
#How to remove the association.
```
subject.page = nil
```

By setting it to nil it will remove the association. And then sure enough, it updates our pages table, it sets the subject_id equal to NULL. Now it's not set anymore.
```
2.2.3 :013 > subject.page = nil
   (38.5ms)  BEGIN
  SQL (58.3ms)  UPDATE `pages` SET `subject_id` = NULL, `updated_at` = '2017-03-27 16:10:51' WHERE `pages`.`id` = 1
   (31.4ms)  COMMIT
 => nil
```

We ask for subject.page
```
2.2.3 :014 > subject.page
 => nil
```
it returns null to us. **Now it removed the relationship, it did not remove the page's record**.


 Page.all, you can see that it's still there.
```
2.2.3 :015 > Page.all
  Page Load (38.9ms)  SELECT `pages`.* FROM `pages`
 => #<ActiveRecord::Relation [#<Page id: 1, subject_id: nil, name: "First Page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 14:41:32", updated_at: "2017-03-27 16:10:51">]>
```
You can see that it's still in our database. **If we wanted to not only break the association, but we wanted to remove the record, well then we would need to use destroy to do that**. So let's reassign it first.
```
2.2.3 :016 > subject.page = first_page
   (1.5ms)  BEGIN
  SQL (26.6ms)  UPDATE `pages` SET `subject_id` = 1, `updated_at` = '2017-03-27 16:13:38' WHERE `pages`.`id` = 1
   (22.8ms)  COMMIT
 => #<Page id: 1, subject_id: 1, name: "First Page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 14:41:32", updated_at: "2017-03-27 16:13:38">
```

You can see it updated it. Once again it has a subject_id of 1.

Now let's try removing it by destroying it,
```
subject.page.destroy
```
```
   (2.0ms)  BEGIN
  SQL (46.0ms)  DELETE FROM `pages` WHERE `pages`.`id` = 1
   (6.4ms)  COMMIT
 => #<Page id: 1, subject_id: 1, name: "First Page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 14:41:32", updated_at: "2017-03-27 16:13:38">
```

Just like we used destroy earlier to delete a record. We're actually calling up that record here by calling subject.page, and then we're telling that object to destroy itself. Hit Return, and sure enough it deletes from pages where pages_id = 2, that of course removes the relationship at the same time.
```
2.2.3 :020 >   Page.all
  Page Load (5.6ms)  SELECT `pages`.* FROM `pages`
 => #<ActiveRecord::Relation []>
```
We can see that the page is gone. Now that we know how to create and work with one-to-one associations, in the next movie let's talk about one-to-many associations.
