In the last movie, we created a rich join between admin users and sections by using section edits. While we gain the ability to add complexity of other attributes and methods inside our section edit's model, we lost something at the same time. We now have to go through an extra step to get from one side of the association over to the other. For example, in our simple, has and belongs to many join, if we wanted to know all of the admin users who could edit a page, we could just request the array of admin users, page dot admin users and we'd get back an array.
```
page.admin_users

[admin_user1, admin_user2]
```
**It would reach across the join table to the other side of the association** but in our rich join, we can't just ask for section's admin users because there is no direct relationship between section and admin user. There's a model that's in between now. We can still do it, we just have to go through that extra step of going through the section edits and looking up each one's admin user.
```
section.admin_users
```
Instead
```
section.section_edits.map {|se| se.admin_user}
[admin_user1, admin_user2]
```

What we really want, is a way to reach across the join easily to be able to access the admin users by just calling section dot admin users like we have in the top line there.

It's clearly possible because the has and belongs to many join does it and we still have the foreign keys in place to make it possible. It's just a question of telling active record about the relationship. So that it can modify the SQL it writes and treat this rich join like a simple join some of the time. Rails gives us a tool for this using ***has many through***. **It allows reaching across a rich join and treating it like it's a has and belongs to many join**. The first step, is to create the regular functional rich join first like we did in the last movie.
```
class AdminUser < ApplicationRecord

  has_and_belongs_to_many :pages
  has_many :section_edits
  has_many :sections, :through => :section_edits
end

```
After that, we can add in another relationship using has many through. So for example, with admin user and section, we already have admin user has many section edits to find. That's the first step. You want to make sure you have that relationship in place. Then once you have that, you can set another relationship for admin user has many sections through section edits.
```
class Section < ApplicationRecord

  belongs_to :page
  has_many :section_edits
  has_many :admin_users, :through => :section_edits
end
```

See how that works? We're telling it that it can reach through section edits to get to the other side of the relationship. Admin user now has many sections.

It's a has many relationship. We have all the same methods and the same functionality we're used to when we have a has many. It's just doing it by going through the section edits' join table and the same thing is true from the other side. A section has many section edits and then we can have section has many admin users through section edits. Remember, you always want to define both sides of the association. Let's try out has many through. Let's begin by going into our admin user model and we have has many section edits.

I'm just going to put a return here. Now we can add another relationship for has many sections, if we go through the section edits. See how that works? Here's our relationship to section edits. Here's our relationship to sections, if we go through the join table that's defined by section edits. Save that file and now let's go into the other side, section.rb, has many section edits is already defined so we want to add has many admin users, if we go through the section edits' join table.

Once both of those relationships are defined, now we can go over to our console and try them out.
```
2.2.3 :001 > me = AdminUser.find(1)
  AdminUser Load (0.4ms)  SELECT  `admin_users`.* FROM `admin_users` WHERE `admin_users`.`id` = 1 LIMIT 1
 => #<AdminUser id: 1, first_name: "Katy", last_name: "McCann", email: "", username: "KT", hashed_password: nil, created_at: "2017-03-28 12:13:34", updated_at: "2017-03-28 12:13:34">
```

Make sure you're in the root of your application, we'll open up our console and let's begin by finding an admin user. I've got me that I created earlier, admin user dot find one. There I am. Let's try me dot section edits.
```
2.2.3 :006 >   me.section_edits
  SectionEdit Load (0.3ms)  SELECT `section_edits`.* FROM `section_edits` WHERE `section_edits`.`admin_user_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy [#<SectionEdit id: 1, admin_user_id: 1, section_id: nil, summary: "Test edit", created_at: "2017-03-28 15:29:01", updated_at: "2017-03-28 15:29:01">]>
2.2.3 :007 >
```
Now, these are the edits that we created in the last movie. Right? This is just the join table and you could see it gives me back and array and the item that's in that array has a class of section edit.
```
2.2.3 :007 > me.sections
  Section Load (2.1ms)  SELECT `sections`.* FROM `sections` INNER JOIN `section_edits` ON `sections`.`id` = `section_edits`.`section_id` WHERE `section_edits`.`admin_user_id` = 1
 => #<ActiveRecord::Associations::CollectionProxy []>
2.2.3 :008 >
```

Now let's try me dot sections because remember, my admin user now has a relationship to sections as well and you can see that it modifies the SQL that it writes. It now creates that join table and it goes through that to find the sections and it returns me a list of these sections. Can actually see that even clearer if we do me dot sections dot first dot name. Right? There's the name of that first section. We can do the same thing from the other side. Let's say that we have section equals section dot first.
```
2.2.3 :034 >   section = Section.first
  Section Load (0.3ms)  SELECT  `sections`.* FROM `sections` ORDER BY `sections`.`id` ASC LIMIT 1
 => nil
```

There's our first section. Now let's call section dot section edits. We get back the section edit that's in between, that's that join table. Now let's try section dot admin users to reach across the join table and to find the list of admin users who've been given permission to edit this section. So using has and belongs to many allows us to treat these rich joins as if it's a has and belongs to many. In practice, most of the time when you're going to use it, is when you're reading back data. When you want to just reach across the join and read what's on the other side.

When you're actually creating data, you probably want to add all those attributes and the richness that's in that rich join in the middle. You probably want to add those. You don't have to. You could go ahead and just add a relationship and it would create it without adding all of the data in between but most of the time since we have that richness there, we probably want to include something. Has and belongs to many allows us to reach across the association and ignore those extra attributes some of the time.
