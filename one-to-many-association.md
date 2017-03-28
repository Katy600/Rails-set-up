# one-to-many associations.
- one-to-many associations are gonna be much more commonly used.
- because we have one object related to many objects, we're gonna use plural names. That's both gonna be true when we define the association and also when we call the methods on it.
- the relationship is going to return an array of objects back to us instead of a single object.
You use a one-to-many association when one object has many objects which belong to it, and which belong to it exclusively.

```
Photographer has_many :photographs
```
For example, a photographer has_many photographs. The photographs all belong to a single photographer, and to that photographer exclusively.

```
Library has_many :books
```
Library has_many books. The books all belong to one particular library. Our simple CMS application is gonna make good use of one-to-many associations. Our subject-page relationship is going to be a one-to-many relationship. Each subject has_many pages, and a page belongs_to a subject. Each page also has_many sections, and each section belongs_to a page. **Whenever you see belongs_to, remember, that's your tip on where to put the foreign key**. We already created the foreign keys for both pages and for sections.

For pages we had subject ID and we worked on that in the last movie. On this sections table, we already have a foreign key called page ID that holds its relationship to page. So we're okay on that point. And also remember that you should always define both sides of the association. Both the has_many and the belongs_to. Once we define these relationships in our model, Rails provides a few methods automatically. This was true with has_one but with has_many, we get more methods. First we get subject.pages.
```
subject.pages
```
It's very similar to subject.page that we had before, **but now it's plural, and of course it returns an array of objects to us**, all of the pages related to a subject.

### If we wanna add a page to that collection, then we use the append operator.
```
subject.pages << page
```
That's what we would normally use to append something onto an array, and it looks like <<. That would take a page object and add it to the subject's pages. We can always use the equals operator, but if we do, then we have to define everything that's in the collection.

```
subject.pages = [page, page, page]
```
o subject.pages equals exactly these three pages. If we want to remove just a single page from the collection, then we do that with subject.pages.delete and then as an argument, we provide the object for the page that we wanna delete.
```
subject.pages.delete(page)
```

We can do this same thing with destroy which will not only remove the association but destroy the page as well.
```
subject.pages.destroy(page)
```
If we wanna remove all pages, then we could just set the equal to an empty array using the equal sign, or you can use clear, and clear will do the same thing.

```
subject.pages.clear
```
If we wanna know whether we've cleared it, and find out if that array is empty, we can use the empty method, just like you can on a normal array,

```
subject.pages.empty
```

and if we wanna know specifically how many items are inside the collection, we can use the size method.

```
subject.pages.size
```
 Let's put a one-to-many association into our simple CMS project.

So, we're gonna start by going into our subject model. Previously, we had had this as has_one. We're gonna change it now to be has_many, which is what we really wanted, we were just demoing it before. And it's has_many pages, notice that it's plural now. It just always fits what reads nicer, it's much easier to read has_many pages, makes sense that there's more than one, and then we'll save that and let's open up the page model and we have belongs_to subject, and we don't need to make any changes here. It's still the same thing. It belongs to the subject.

It tells active record that this record has a foreign key and that foreign key can be used to figure out what its parent is. That was true in the one-to-one and that's also true with one-to-many. Once we have those defined, let's go over to our command line, and from the root of our project let's launch our Rails console. That will launch the console, and then we'll have subject=subject.find(1)
```
subject = Subject.find(1)
```
```
  Subject Load (33.8ms)  SELECT  `subjects`.* FROM `subjects` WHERE `subjects`.`id` = 1 LIMIT 1
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
```

and then let's try subject.page.
```
Subject.page
```

 You'll remember we did this in the last movie. Subject.page If I hit return, I get back an error, because page is no longer a method, undefined method page.

 ```
NoMethodError: undefined method `page' for #<Class:0x007fde029dbb78>
 ```
**That was a method that was created when I had a has_one relationship**. ***Now, it's going to be pages***
```
2.2.3 :004 > subject.pages
  Page Load (10.1ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`subject_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy []>
```
**plural**. So subject.pages and it returns the set of pages that match. Notice it goes out and issues an SQL statement to figure it out, but the end result is that it's just an empty array, because there are no pages at the moment.

## So, let's create a page.
```
first_page = Page.new(:name => "First_page", :permalink => "first", :position => 1)
```
First_page = page.new, name, make it first page. This is very similar to what we did before. We'll have permalink.

So it's gonna be first, and position is gonna be one. Okay, so now that I've got my page object,
```
2.2.3 :008 > first_page = Page.new(:name => "First_page", :permalink => "first", :position => 1)
 => #<Page id: nil, subject_id: nil, name: "First_page", permalink: 0, position: 1, visible: false, created_at: nil, updated_at: nil>
2.2.3 :009 >
```
notice it is a new page object, I did not actually save it to the database.
## Now let's call subject.pages and let's add that first page to it.
```
2.2.3 :009 > subject.pages << first_page
   (0.3ms)  BEGIN
  SQL (27.3ms)  INSERT INTO `pages` (`subject_id`, `name`, `permalink`, `position`, `created_at`, `updated_at`) VALUES (1, 'First_page', 0, 1, '2017-03-27 17:01:43', '2017-03-27 17:01:43')
   (1.9ms)  COMMIT
 => #<ActiveRecord::Associations::CollectionProxy [#<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">]>
2.2.3 :010 >
```

We're using that append operator to append this object onto it. And notice that it issues and SQL statement to insert the pages into the database. It actually saves the record at the same time as it adds it into the collection.

So now, if we go up and ask for subject.pages,
```
.2.3 :010 > subject.pages
 => #<ActiveRecord::Associations::CollectionProxy [#<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">]>
2.2.3 :011 >
```

you'll see that we get back an array that has that object in it, and that is a saved object in the database.

```
2.2.3 :011 > first_page.new_record?
 => false
```
False, it's not a new record. It has been saved.

## Let's add a second page now.
```
2.2.3 :012 > second_page = Page.new(:name => "Second Page", :permalink => 'second', :position => 2)
 => #<Page id: nil, subject_id: nil, name: "Second Page", permalink: 0, position: 2, visible: false, created_at: nil, updated_at: nil>
2.2.3 :013 >

```
And now let's add that one to it.
```
2.2.3 :013 > subject.pages << second_page
   (1.2ms)  BEGIN
  SQL (13.4ms)  INSERT INTO `pages` (`subject_id`, `name`, `permalink`, `position`, `created_at`, `updated_at`) VALUES (1, 'Second Page', 0, 2, '2017-03-27 17:12:10', '2017-03-27 17:12:10')
   (1.2ms)  COMMIT
 => #<ActiveRecord::Associations::CollectionProxy [#<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">, #<Page id: 3, subject_id: 1, name: "Second Page", permalink: 0, position: 2, visible: false, created_at: "2017-03-27 17:12:10", updated_at: "2017-03-27 17:12:10">]>
2.2.3 :014 >
```
now let's just try calling subject.pages.
```
subject.pages
 => #<ActiveRecord::Associations::CollectionProxy [#<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">, #<Page id: 3, subject_id: 1, name: "Second Page", permalink: 0, position: 2, visible: false, created_at: "2017-03-27 17:12:10", updated_at: "2017-03-27 17:12:10">]>
2.2.3 :015 >
```
# Size
You can see that it returns an array and that array has two pages in it, page ID one and page ID two. Let's try subject.pages size.
```
2.2.3 :015 > subject.pages.size
 => 2
```
You see it comes back and tells me that there's two.
# Count
I'll say subject.pages count,
```
2.2.3 :016 > subject.pages.count
   (21.6ms)  SELECT COUNT(*) FROM `pages` WHERE `pages`.`subject_id` = 1
 => 2
```

and it does the same thing but actually issues an SQL statement, just a slightly different version.

They both do the same thing though. This one is telling me the size of the array, this one is telling it to go and count the records in the database.

# Empty
Let's try subject.pages empty
```
2.2.3 :017 > subject.pages.empty?
 => false
```
question mark. 'Course it's not empty.

Next let's try deleting a record.

# Delete
```
2.2.3 :018 > subject.pages.delete(second_page)
   (1.2ms)  BEGIN
  SQL (7.1ms)  UPDATE `pages` SET `pages`.`subject_id` = NULL WHERE `pages`.`subject_id` = 1 AND `pages`.`id` = 3
   (17.3ms)  COMMIT
 => [#<Page id: 3, subject_id: 1, name: "Second Page", permalink: 0, position: 2, visible: false, created_at: "2017-03-27 17:12:10", updated_at: "2017-03-27 17:12:10">]
```
Now we only have one record that's in there. Subject.pages, it's ID one. Notice that it deleted it here, and it did that by making an update statement to the database, and it returned the object that it deleted as well.

So that object is what got returned to me as a result of that operation. And then, last of all, let's do

subject pages.size again,
```
2.2.3 :020 > subject.pages.size
 => 1
```

and you'll see that now it's equal to one. And last of all, let's look at this from the other side of things. Let's take our first page and ask for its subject,

```
2.2.3 :021 > first_page.subject
 => #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">
2.2.3 :022 >
```

 and you'll see that it works exactly the same way as it did for the one-to-one relationship. A first page knows what its subject is. On your own, try adding the one-to-many association that we need for page and section. Page has_many sections, and section belongs_to page.
