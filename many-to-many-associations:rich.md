We've seen how to create simple many-to-many associations using has_and_belongs_to_many. In this movie, we're gonna ***add more complexity to our join tables***. I call these **rich joins**. First, to understand why we need this complexity, let's go back to the many-to-many diagram for the beginning of this chapter. This is a classic many-to-many example. We have a number of courses, we have a number of students taking these courses. There will be a table named courses and a table named students, and in the simple join, we would have a join table between them, which we would call courses_students.

In the join table will be two foreign keys, one for the course, and one for the student. Here's where the simple join falls short, though. ***Imagine that we wanna keep track of more information than just the fact that a student is participating in a class***. You might wanna keep track of their seat number, or the date they signed up, or the date they last attended, or how many times they've been absent. That information belongs in this join table, ***'cause the join table is essentially the object maintaining the presence of the student in the course***. Everything that has to do with Michael and his participation in Chemistry should be stored in that join.

This is when we need to switch to a rich join. One of **the benefits of a rich join is that we no longer have to follow Rails table-naming conventions**. Instead, it's a good idea to **give it a name that describes the join**. I've named it course_enrollments. You could just as easily name it student_enrollments. We're not just renaming the table, though. ***We're also gonna be creating a new model for our course_enrollments***. Instead of using **has_and_belongs_to_many between course and student, now course will have many course_enrollments**, and so will students.

```
Course
----------------
has_many :course_enrollments
```
```
Student
-----------------
has_many :course_enrollments
```
```
CourseEnrollment
-----------------
belongs_to :course
belongs_to :student
```
CourseEnrollment is going to have two belongs_to definitions, one for course and one for student. In the previous movie, you'll remember that I said that many-to-many associations work just like has_many associations? Well, here, that's actually true. They are has_many associations now. CourseEnrollment still serves as a join between them. ***It's just now a join that can have more richness***. It can have **more columns in the database, more attributes, and more custom methods stored in that model**. We use rich joins for the same reason that we use a simple join.

The difference is only that we get more complexity on the join. Because of that, we need to change the way that we store this relationship in the database. ***We're going to have a join table still***, **with two indexed foreign keys, but because it has a model for the join, it's also going to require having a primary key defined**, so **we will need to have an (:id)** now, **so that we can create this record, find it, update it, and delete it, just like we can with any active record object**. There's no table-naming convention that we need to follow, however, one helpful tip is that rich join table names often will end in "-ments" or "-ships", such as "assignments", "engagements", "authorships", "memberships".

If you find yourself trying to think of a good name, remember that names with those endings often work well. In our application, we will use rich joins with AdminUser and Section. The idea is that every time an AdminUser updates a Section of the CMS, we're gonna keep track of the edit in a table called section_edits. We'll have a model for it called SectionEdit. Then we'll have...
```
AdminUser has_many :section_edits

SectionEdit belongs_to :admin_user
```
Then on the other side of the join, we'll have

```
Section has_many :section_edits

SectionEdit belongs_to :section
```

Just like before, we wanna make sure that we define both sides of the association. Now that we've talked about this in theory, let's put it into practice. Let's start with the database.

From the root of my project...
```
rails generate model SectionEdit
```
I'll use rails generate, and I'm gonna create a new model called SectionEdit. This will create a number of files for me, including my models file for section_edit, but also in migration.
```
katymccann (master) simple_cms $ rails generate model SectionEdit
Running via Spring preloader in process 78403
Expected string default value for '--jbuilder'; got true (boolean)
      invoke  active_record
      create    db/migrate/20170328141249_create_section_edits.rb
      create    app/models/section_edit.rb
      invoke    test_unit
      create      test/models/section_edit_test.rb
      create      test/fixtures/section_edits.yml
```

Let's go and find that migration now. Inside my db/migrate folder, you can see that it is a change method.
```
class CreateSectionEdits < ActiveRecord::Migration[5.0]
  def change
    create_table :section_edits do |t|

      t.timestamps
    end
  end
end
```
We're gonna change that to be up, and we'll have one down here. We always wanna have the reverse, down. If we create a table up there, we want to drop the table down here, :section_edits. Now we need to have our definition for section_edits. Remember, the most important things in the join table is that it has the two foreign keys, so we're gonna want to have integer "admin_user_id" and t.integer "section_id". That's still the same as what we were doing in the last movie, but now, we have the ability to add more columns.
```
class CreateSectionEdits < ActiveRecord::Migration[5.0]
  def up
    create_table :section_edits do |t|
      t.integer "admin_user_id"
      t.integer "section_id"
      t.string "summary"
      t.timestamps
    end
    add_index("section_edits", ["admin_user_id", "section_id"])
  end
  def down
    drop_table :section_edits
  end
end

```

For example, we can have a string that stores a "summary" of what kind of edit was being made. We also have our timestamps here. We didn't have that before. Now we have timestamps so we can see when this section_edit was first created, and when it was last updated. We have two foreign keys here. It's also still true that we wanna index those, so we wanna make sure that we still add our index, index on "section_edits", and we're gonna have two foreign keys, so we'll put them inside an array, 'admin_user_id' and 'section_id'.

Now, in the last movie, we also added an option here to set id equal to false, but here, we want the id. This is gonna be an ActiveRecord object, and anytime we have an ActiveRecord-based model, ActiveRecord will insist that it has an id, so we'll wanna make the id at this time. Let's save that file, come back over here, run...
```
rails db:migrate
```

```
katymccann (master) simple_cms $ rails db:migrate
== 20170328141249 CreateSectionEdits: migrating ===============================
-- create_table(:section_edits)
   -> 0.1331s
-- add_index("section_edits", ["admin_user_id", "section_id"])
   -> 0.0448s
== 20170328141249 CreateSectionEdits: migrated (0.1780s) ======================

```
OK, now that it's been created, ***let's come over here and let's define those associations***. Let's start with AdminUser.

So inside the AdminUser, we're gonna have has_many :section_edits.
```
class AdminUser < ApplicationRecord

  has_and_belongs_to_many :pages
  has_many :section_edits
end

```
Then let's go into section_edits.
```
class SectionEdit < ApplicationRecord

  belongs_to :admin_user
  belongs_to :section
end

```
We're gonna have the reverse of that, which is that it belongs_to an :admin_user. OK, so that's one side of it. Now, let's think about the other side. The other side is also gonna have belongs_to, and this time, it's going to be a :section, then

let's jump over to section,
```
class Section < ApplicationRecord

  belongs_to :page
  has_many :section_edits
end

```

section will have has_many :section_edits.

There we go, actually have two different associations that allow me to make this join using three different models. Save all of it, and let's go try it out. Let's go to my command line, and I'm gonna run rails console.

Once that opens up, I'm gonna find AdminUser, I'd already created that in the last movie, AdminUser.find(1).
```
2.2.3 :001 > me = AdminUser.find(1)
  AdminUser Load (27.5ms)  SELECT  `admin_users`.* FROM `admin_users` WHERE `admin_users`.`id` = 1 LIMIT 1
 => #<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">
2.2.3 :002 >
```
That should return my AdminUser, and let's ask for

```
me.section_edits
```
```
  SectionEdit Load (32.3ms)  SELECT `section_edits`.* FROM `section_edits` WHERE `section_edits`.`admin_user_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy []>
2.2.3 :003 >
```
You can see that it goes to the database, to the section_edits table, and looks for associations that are there, and there are none.

Didn't find anything. It never actually had to go all the way and look in the sections table. It was enough just to look in the section_edits. All right, now let's try a section. We don't have any sections yet, so let's create one. Section.create (:name, we'll just call it 'Section One' and we'll give it a :position of 1. OK, so now I have a section saved to my database. Now let's call section.section_edits and see what it's got.

```
section = Section.create(:name => "Section One", :position => 1)
```
```
   (0.2ms)  BEGIN
   (0.2ms)  ROLLBACK
 => #<Section id: nil, page_id: nil, name: "Section One", position: 1, visible: nil, content_type: nil, content: nil, created_at: nil, updated_at: nil>
```

```
2.2.3 :004 > section.section_edits
 => #<ActiveRecord::Associations::CollectionProxy []>
2.2.3 :005 >
```


It has none. Again, I'm gonna check that ***section_edits table, and it came back with an empty array.*** So **before, we were working with our simple join. The two join objects interacted with each other**, and **Rails managed the simple join for us**, adding or deleting rows as necessary. With the **rich join, we have a model that we wanna work with to manage that join ourselves**, so **in order to work with this join, we wanna create a new edit**. I'll call it
```
edit = SectionEdit.new
```
```
=> #<SectionEdit id: nil, admin_user_id: nil, section_id: nil, summary: nil, created_at: nil, updated_at: nil>
```
```
2.2.3 :006 > edit.summary = "Test edit"
 => "Test edit"
```
gonna work with it just like a normal model, edit.summary, and let's say it's a "Test edit" and now I need to attach it, so


```
section.section_edits.
```
```
2.2.3 :007 > section.section_edits << edit
 => #<ActiveRecord::Associations::CollectionProxy [#<SectionEdit id: nil, admin_user_id: nil, section_id: nil, summary: "Test edit", created_at: nil, updated_at: nil>]>
2.2.3 :008 >
```
Let's add our edit in there. Now it didn't work. And if you remember, in a previous movie, I told you that **belongs_to is no longer optional**. Let's go back and just take a quick look at that model for section_edit.
```
class SectionEdit < ApplicationRecord

  belongs_to :admin_user
  belongs_to :section
end

```
```
2.2.3 :004 > edit = SectionEdit.new
 => #<SectionEdit id: nil, admin_user_id: nil, section_id: nil, summary: nil, created_at: nil, updated_at: nil>
2.2.3 :005 >
```

```
.2.3 :006 > edit.summary = "Test Edit"
 => "Test Edit"
2.2.3 :007 >
```

```
.3 :007 > section.section_edits << edit
 => #<ActiveRecord::Associations::CollectionProxy [#<SectionEdit id: nil, admin_user_id: nil, section_id: nil, summary: "Test Edit", created_at: nil, updated_at: nil>]>
2.2.3 :008 >
```
```
2.2.3 :010 >   me = AdminUser.find(1)
  AdminUser Load (0.4ms)  SELECT  `admin_users`.* FROM `admin_users` WHERE `admin_users`.`id` = 1 LIMIT 1
 => #<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">
2.2.3 :011 > edit = SectionEdit.new(:summary => "Test Edit", :admin_user => me, :section => section)
 => #<SectionEdit id: nil, admin_user_id: 1, section_id: nil, summary: "Test Edit", created_at: nil, updated_at: nil>
2.2.3 :012 >
```
It has two belongs_to. **Neither one of those is optional**. When I try to save this, when I try to add it to section_edits, remember when I added to it and it saves it automatically? We saw that back with has_many. So when it tries to save it, it now would have, potentially, a section attached to it, but it would still not have an admin_user.
```
2.2.3 :003 > section.section_edits
 => #<ActiveRecord::Associations::CollectionProxy []>
2.2.3 :004 >
```

```
2.2.3 :012 > me.section_edits
  SectionEdit Load (0.3ms)  SELECT `section_edits`.* FROM `section_edits` WHERE `section_edits`.`admin_user_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy [#<SectionEdit id: 1, admin_user_id: 1, section_id: nil, summary: "Test edit", created_at: "2017-03-28 15:29:01", updated_at: "2017-03-28 15:29:01">]>
2.2.3 :013 >
```

```
2.2.3 :014 > section.section_edits
 => #<ActiveRecord::Associations::CollectionProxy [#<SectionEdit id: nil, admin_user_id: nil, section_id: nil, summary: "Test Edit", created_at: nil, updated_at: nil>]>
2.2.3 :015 >
```
All right, we have to have both of those be true, or else we can't save them. So one good way to do this is just to specify it right from the beginning. SectionEdit.new, and let's have the :summary. This time, we'll put it right here when we create it, 'Test edit', and then let's specify the :admin_user. It's going to be me, and the :section is going to be section. Now I have an edit that I can call edit.save, and it will save into the database.

You can see it did the insertion, and you can see that it returned true, so be careful about that. Remember that belongs_to is no longer optional whether or not you have a parent relate to that. It's especially a problem when you're working with these join tables because there's more than one parent. Now if we come back over and we ask me for my section_edits, I didn't get anything back. Let's try me.section_edits(true), and that's going to reload all of the section_edits.

It's gonna say go back to that database, don't use your cached version, go back and ask again. See if a new addition has been added, and it has. So you will have to be careful about that some of the time. When you use the append notation, Rails keeps track of that addition for you and it knows about it, but when we created it by just going straight to the section table, it did not make any updates to the other related objects at the same time. We have to reload them by using that (true) at the end. And last of all, let's do the same thing with section.section_edits.

You can see that we do have the section_edit there, and that's because Rails has since reloaded it so it now knows about this section edit that's there. In the next movie, let's talk about how we can traverse across the rich association we've just learned to create.
