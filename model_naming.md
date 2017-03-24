

## When you want to generate a model
- you go to the command line at the root of your Rails application, and you type...
```
rails generate model SingularName
```

For example...
```
rails generate model Subject
```
It creates a migration file for you, in DB/migrate, and it gives it a filename, which is automatically named create underscore subjects.
```
File name: 27478538784959684_create_subjects.rb

Class name: CreateSubjects

```

*Notice that it is plural*. That file has a **class name inside of CreateSubjects**. Also notice that that's *plural*. Then, the **table that we're going to be creating is also going to be called subjects**.

```
create_table :subjects
drop_table :subjects
```
**The generator will also create a file in your models directory**, and that one will be called subject dot RB, singular.
```
app/models
```
```
File name: subject.rb
```
If we look inside it, we'll see that it defines a class name, which is Subject, and that's going to be singular, and Camel case, just like what we provided from the command line. The pattern to these names is not a coincidence.
```
Class name: Subject
```

In fact, the **naming is important**. Table names, file names, and class names matter a great deal in Rails. If we name things the way that Rails prefers, then Rails can use its sensible defaults to find files and code without us having to explicitly tell it where to find them. For example, if we write code that needs to use a model called subject, then Rails will automatically look for a definition in the file, app, models, subject dot RB. If our code asked that subject class to retrieve data from the database, then Rails defaults will tell it to look for a table called subjects, the down cased plural of the class name.

We can configure all of these names when needed, but it speeds up development if we stick with Rails conventions most of the time. I also want to point out that the Rails generate command that we used isn't doing anything magical for us. All it's doing is creating files with everything named according to the Rails conventions. You could create these exact same files manually, and as long as you followed Rails naming conventions, you would have the exact same results. I just want to demystify that. There's nothing extra that it's doing, it's just helpfully doing the work of creating the files with the expected names for you.

Let's take a look at the models we generated already. Your model files should always reside inside app, and inside the models directory. In there, you can see the different models I've created already, user dot RB, subject dot RB, page dot RB, and section dot RB. Let's open up the user dot RB.
```
class User < ApplicationRecord
end

```
It's a simple Ruby class, called user. It inherits from ApplicationRecord. ApplicationRecord Is actually just another file, which is right here in the models directory. If we open that up, you will see that all it really does is inherit from ActiveRecord base.
```
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true
end

```

Don't worry too much about this line right now. ApplicationRecord is going to be a place where we can put code that is common to all of our models. We will talk about it more later. The important thing to know is this ActiveRecord base Is the core part of Rails where we are adding behaviors to our class. User is able to do a lot of things using the ActiveRecord design pattern, by inheriting from ActiveRecord base. You could have non-ActiveRecord models too, they just wouldn't inherit from anything, and then it would be a standalone Ruby class.

You typically wouldn't create a migration for that, because there is no database table that corresponds to the model. In this case though, I have user, which is an ActiveRecord model. Now, my file name here, user dot RB, does match my class name, that's important, But if you recall, in the last chapter, one of the changes that we made in our migrations is that we changed our table name from user to admin users. Let's just take a quick look here.

```
class AlterUsers < ActiveRecord::Migration[5.0]
  def up
    rename_table("users", "admin_user")
    add_column("admin_user", "username", :string, :limit => 25,
    :after => "email")
    change_column("admin_user", "email", :string, :limit => 100)
    rename_column("admin_user", "password", "hashed_password")
    puts "*** Adding an index ***"
    add_index("admin_user", "username")
  end

  def down
    remove_index("admin_user", "username")
    rename_column("admin_user","hashed_password", "password" )
    rename_column("admin_user", "password", "hashed_password")
    change_column("admin_user", "email", :string, :default => '', :null => false)
    remove_column("admin_user", "username")
    rename_table("admin_user", "users")
  end
end

```

That's in DB, inside migrate, inside alter, we changed table users to be admin users.

We did that after we generated this file. Let me just close these up, and let's go back to user.

By doing that, we've now gone against Rails naming conventions. When our user class tries to work with the database, it's going to automatically look for a table called users, and it won't find one. Instead, we will get an error. We don't have to follow the Rails naming conventions, but if we stray from them, we do have to add configuration so it knows where to find things. There are two possible solutions here. The first is that we can tell our class where it should find its table.
```
class User < ApplicationRecord
  self.table_name = "admin_user"
end
```
The way that we use a different table name is that we just add a line, self dot table underscore name equals, and we tell it what the table name is, admin users. Now, it will be able to find our table. This is especially useful if you have legacy databases that don't follow the Rails conventions. You want to use some simple sort of updated model names, but you want to use whatever the legacy database name is. Maybe it's called something like LRV admin users, right, something crazy.
```
class User < ApplicationRecord
  self.table_name = "lrv_admin_user"
end
```
We want to still use it, but we don't want to call our model whenever that happens to be. That's one way we could do it. We also could do it another way, which is that we can change, I'll comment this out, our class name to be AdminUser,
```
class AdminUser < ApplicationRecord
  self.table_name = "admin_user"
end
```
but if I do that, I also have to rename this file. You may have to go to the Finder, or the operating system in order to change it. In the Atom text editor, I can also hold down the control key while I click on the file, and I can choose rename, and it will give me the chance to rename the file as admin underscore user.

Now, things are back in sync again, and I don't actually need to specify the table name, because admin users is going to be the default it will pick up. I've got everything back in sync again. Again, the names of files, classes, and tables are not an accident, and you need to stick with the Rails conventions, or else add your own configurations whenever you go against them.
