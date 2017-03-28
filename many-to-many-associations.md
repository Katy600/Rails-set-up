## Many-to-many associations.

We'll begin by looking at a simple join, and then move on to a more complex join in the next movie. **Many-to-many associations are similar to one-to-many associations because they also have an object which has many objects which belong to it**. The difference that **as a many-to-many, the objects don't belong to it exclusively**. For example,

```
Project has_and_belongs_to_many :collaborators.
```

The project can have many collaborators, but the collaborators can also have many projects.

It's not like the project owns a collaborator exclusively. They're allowed to work on other projects, too. You have a
```
BlogPost has_and_belongs_to_many :categories.
```

If our BlogPost is both in the technology and training categories, it doesn't mean that no other BlogPost can be in the technology and training categories. Technology will have many BlogPosts. Training will have many BlogPosts. And several BlogPosts can make use of those other categories. When we talk about many-to-many, **we're essentially talking about having two different sets: a set of projects and a set of collaborators**.

And then we have a mix and match of relationships between those. It's gonna be very similar to working with a one-to-many association, but **it's a one-to-many association from both sides at the same time**. And as we saw back in the introduction to the chapter, **that creates a problem as to where to put the foreign key**. **We need to put it in a join table**, and **the join table is going to hold two foreign keys that are going to relate to the two different sides of the association**. We're gonna **put an index on both of those keys so that we can look them up very quickly**.

The table doesn't need to have any kind of primary key on it because we're not gonna be looking up those records directly. We're only gonna be using them as links to maintain the relationship between two other classes. When we add has_and_belongs_to_many in our models, Active Record will add instance methods that are just like the ones that are used by one-to-many. And this makes sense because we're still working with arrays of objects on both sides. So the methods will be the same, and Rails will handle all the behind-the-scenes differences for us. In our simple CMS, we're gonna use many-to-many relationships between AdminUser and page.

What I have in mind is that certain AdminUsers will be able to edit a page. So there will be a relationship between a page and the users who can edit the page. It won't be an exclusive relationship because each of those users also has the ability to edit other pages. So we have
```
AdminUser has_and_belongs_to_many :pages

Page has_and_belongs_to_many :admin_users
```

Just like with our other associations, be sure that you define both sides of the relationship so that you can move both ways from AdminUser to pages and from page back to AdminUsers.

Before we can work on our models, we're going to need to create a migration for the join table used in this relationship. You can name your join table anything you want, but Rails has a convention that we should try and follow. Then you can configure it if you wanna use something different.

**The Rails convention is to use the name of the first table being joined, then an underscore, and then the second table name**.
```
first_table + _ + second_table
```
**Both table names are gonna be plural, and they're gonna be expressed in alphabetical order**. That's important. Again, this is the default naming, and it can be configured.

**If we had Project and Collaborator...**
```
Project-Collaborator

collaborators_projects
```
and those were our two classes, then presumably we have a Projects table and a Collaborators table, then the join table is gonna be collaborators_projects, collaborators being the first in alphabetical order, and then underscore, and then the second one, projects.

```
 BlogPost and Category

 blog_posts_catagories
```
Again, alphabetical order.

```
AdminUser-Page

admin_users_pages
```

Just make sure they're both plural and in alphabetical order.

## Let's create a migration for this join table now.
```
rails generate migration CreateAdminUsersPagesJoin
```
It can really be anything we want. We want it to be something that's descriptive.

```
katymccann (master *) simple_cms $ rails generate migration CreateAdminUsersPagesJoin
Running via Spring preloader in process 68998
Expected string default value for '--jbuilder'; got true (boolean)
      invoke  active_record
      create    db/migrate/20170328092254_create_admin_users_pages_join.rb
katymccann (master *) simple_cms $
```

Let's now go in and open that up. That's in our db directory, inside migrate, and it's gonna be the most recent one down here,
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def change
    create_table :admin_users_pages_join do |t|
    end
  end
end

```


and you can see that it already gave us a create_table method built in here. Had we named it something else besides create, it would not have done that automatically, but ***create is sort of a key word to the migration to tell it that we wanna create a table***. Now I didn't name our table quite correctly because I put join after it. **If I had left join off, it would've worked, but there's the table name that we wanna create, admin_users_pages**.
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def change
    create_table :admin_users_pages do |t|
    end
  end
end

```
I'm not gonna do this in a change method, I'll do it in an up method so that down here, I can have a down method, and that down method is going to drop the table, I'm just gonna copy that, paste it in.
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def up
    create_table :admin_users_pages do |t|
    end
  end

  def down
    drop_table :admin_users_pages
  end
end
```
Okay, so we're gonna create the table, and then we're gonna drop the table.



So now we need to have our table definition here. We're gonna need to have two foreign keys on the table: t.integer and then "admin_user_id" will be the first one, and then t.integer and "page_id".
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def up
    create_table :admin_users_pages do |t|
      t.integer "admin_user_id"
      t.integer "page_id"
    end
  end

  def down
    drop_table :admin_users_pages
  end
end

```

Both of those are singular because it's referring to a single record each time.


Okay, there's two other things that we need to take care of with the join table, besides just creating the table and putting the foreign keys on it. ***We need to have an index***, and ***that index needs to be on both of those keys together***.
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def up
    create_table :admin_users_pages do |t|
      t.integer "admin_user_id"
      t.integer "page_id"
    end
    add_index("admin_users_pages", ["admin_user_id", "page_id"])
  end

  def down
    drop_table :admin_users_pages
  end
end

```

So add_index, and then just copy this table name here, put that in, and then ***we're gonna be using array, and we're gonna put both of these indexes in there***. First one, and the second one, I'll paste in there.

It doesn't really matter which order you do it in, I'm gonna put it with AdminUser first, but **it's gonna create an index on both of these columns together**. ***That's important with the join table***. The other thing that we've gotta remember is that **we don't need an id on this table**. We're not gonna actually be instantiating this at any point, **there's no time that we're gonna bring up an AdminUsers-Pages object**, **it's just gonna be used as a quick join between the two**. So to do that, we wanna make sure that we ***add id false as an option to the creation***. That will **turn off the automatic id creation**.
```
class CreateAdminUsersPagesJoin < ActiveRecord::Migration[5.0]
  def up
    create_table :admin_users_pages, :id => false do |t|
      t.integer "admin_user_id"
      t.integer "page_id"
    end
    add_index("admin_users_pages", ["admin_user_id", "page_id"])
  end

  def down
    drop_table :admin_users_pages
  end
end
```
So those are the important things, ***we want the foreign keys, we want an index on those foreign keys, and we don't want to have an id***. Let's save that migration, and then let's go to our command line,
***let's run***

```
rails db:migrate
```

Alright, it ran successfully.
```
katymccann (master *) simple_cms $ rails db:migrate
== 20170328092254 CreateAdminUsersPagesJoin: migrating ========================
-- create_table(:admin_users_pages)
   -> 0.1245s
-- add_index("admin_users_pages", ["admin_user_id", "page_id"])
   -> 0.1125s
== 20170328092254 CreateAdminUsersPagesJoin: migrated (0.2371s) ===============

```

Now let's come over here, and in our models, let's define these associations. So inside AdminUser, we're going to need to create one. This one's going to be a has_and_belongs_to_many :pages, plural.

```
class AdminUser < ApplicationRecord

  has_and_belongs_to_many :pages
end
```
***let's go into page***, and right after belongs_to, let's add has_and_belongs_to_many :admin_users.

```
class Page < ApplicationRecord

  belongs_to :subject {:optional => false}
  has_and_belongs_to_many :admin_users, :join_table => "pages_admin_users"

end

```
Now if we wanted to specify the join table for this, we could do this on both of them, :join_table, and then we could specify whatever the name was. Let's say we had called it pages_admin_users, right? Not in alphabetical order. Then ***that would tell it where the join table was***.

***But we're using Rails conventions***, so we don't need to specify the join table name.

```
class Page < ApplicationRecord

  belongs_to :subject {:optional => false}
  has_and_belongs_to_many :admin_users

end

```
Alright, let's go back over here and let's launch our console and let's try out our relationship. I think it's always a good idea to sort of exercise these relationships in your console when you're first starting out. So let's begin by looking up our AdminUser. AdminUser.all.
```
2.2.3 :001 > AdminUser.all
  AdminUser Load (11.4ms)  SELECT `admin_users`.* FROM `admin_users`
 => #<ActiveRecord::Relation []>
2.2.3 :002 >
```
#### I originally got an error saying
```
AdminUser Load (0.7ms)  SELECT `admin_users`.* FROM `admin_users`
ActiveRecord::StatementInvalid: Mysql2::Error: Table 'simple_cms_development.admin_users' doesn't exist: SELECT
```
#### When I looked inside mysql database then the table was listed as 'admin_user'. I didn't know how to fix this with Rails or where I had gone wrong, so I ended up just changing the name inside the database to 'admin_user'. This fixed my error message.

 We don't have any AdminUser so let's create one, me. You can create one for yourself, AdminUser.create, I'm gonna give it a :first_name, is gonna be Kevin, and :last_name will be Skoglund, and let's give it a :username, which will be kskoglund.
 ```
 2.2.3 :004 >   me = AdminUser.create(:first_name => "Katy", :last_name => "McCann", :username =>"KT")
   (26.7ms)  BEGIN
  SQL (78.8ms)  INSERT INTO `admin_users` (`first_name`, `last_name`, `username`, `created_at`, `updated_at`) VALUES ('Katy', 'McCann', 'KT', '2017-03-28 12:13:34', '2017-03-28 12:13:34')
   (44.7ms)  COMMIT
 => #<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">
 ```
Okay, so it created my AdminUser for me when I called create. Now I'm gonna just clear that page. Let's get a page our of our database.
```
page = Page.find(2)
```
```
Page Load (28.5ms)  SELECT  `pages`.* FROM `pages` WHERE `pages`.`id` = 2 LIMIT 1
 => #<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">
2.2.3 :006 >
```

So there's Page with id: 2, as my second page. Might be a different id for you. You can always do page.all if you wanna find the page that you want. And then let's do
```
page.admin_users
```
```
  AdminUser Load (71.2ms)  SELECT `admin_users`.* FROM `admin_users` INNER JOIN `admin_users_pages` ON `admin_users`.`id` = `admin_users_pages`.`admin_user_id` WHERE `admin_users_pages`.`page_id` = 2
 => #<ActiveRecord::Associations::CollectionProxy []>
2.2.3 :007 >
```
and we'll see the page's current user. Right, **it's empty, there are none that are assigned yet**, but you'll see that it did go out and it executed a bit of SQL here.

And it's a bit of tricky SQL, it actually has a JOIN clause in it as well. So it went out and **it did a JOIN through our join table in order to find the AdminUsers that belong to this page**. Alright, so now let's add some AdminUsers,

```
page.admin_users << me
```
 and we're gonna use that same notation that we used with has_many. Remember, it really works the same way. This has many AdminUsers, so we could just take me and add myself to that collection, and it will insert a join into the AdminUsers table.

 ```
    (27.2ms)  BEGIN
   SQL (77.2ms)  INSERT INTO `admin_users_pages` (`admin_user_id`, `page_id`) VALUES (1, 2)
    (39.8ms)  COMMIT
  => #<ActiveRecord::Associations::CollectionProxy [#<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">]>
 2.2.3 :008 >
 ```
That's our **join table, and then it joined the id for admin_user_id and page_id.** *We could see the values here, 1 and 2*. So even though we interacted with it like it was a has_many relationship, it knew that it was a has_and_belongs_to_many relationship and performed the necessary SQL behind the scenes.

Now we can say...
```
page.admin_users
```
... and we get back the user we would expect.
```
 => #<ActiveRecord::Associations::CollectionProxy [#<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">]>
2.2.3 :009 >
```
If we do...
```
page.admin_users.empty?
```
we see that it is not empty
```
 => false
```

if we ask it for the size,
```
page.admin_users.size
```
```
 => 1
```
it'll come back and tell us that it's 1.

and we can take me now and ask for my pages,
```
me.pages
```
```
  Page Load (36.7ms)  SELECT `pages`.* FROM `pages` INNER JOIN `admin_users_pages` ON `pages`.`id` = `admin_users_pages`.`page_id` WHERE `admin_users_pages`.`admin_user_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy [#<Page id: 2, subject_id: 1, name: "First_page", permalink: 0, position: 1, visible: false, created_at: "2017-03-27 17:01:43", updated_at: "2017-03-27 17:01:43">]>
```

and it will find out what my pages are. It does the same thing, it does a JOIN, looks through that join table in order to figure out what pages belong to me. And here it is, it returned an array and now I have one page that belongs to me. Using this join table allows us to make non-exclusive many-to-many relationships.
