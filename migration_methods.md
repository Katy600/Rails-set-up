## Migrations allow us to perform mini-database operations.


## Table migration methods.
```
create_table(table, options) do |t|

end
```
```
drop_table(table)
```
```
rename_table(table, new_name)
```

There's one more which is rename_table, where you provide the old table name as the first argument, and the new table name as the second one.

There are also column migration methods, such as add_column, remove_column, rename-column and change_column.

```
add_column(table, column, type, options)
```
```
remove_column(table, column)
```
```
rename_column(table, column, new_name)
```
```
change_column(table, column, type, options)
```

All four of these methods take the table name and the column name as their first two arguments.

When we're working with create_table, we didn't have to specify table name each and every time we wanted to create a column. But here, because it's a stand alone method call, we do need to tell it which table we're referring to. With add_column, we are adding a column with a type definition the same way we did when we created table, but we can't use that short format that we had with create_table, we have to use the longer format where we specify the type explicitly as one of the arguments. The options add_column are the same that we saw for create_table: limit, default, null, et cetera. I think remove_column and rename_column are self-explanatory, and change_column is used for changing the definition or the options on an existing column that already has data in it.

We wouldn't want to remove that column and re-add it, because that would wipe out all the data inside of it. Instead, we want to change the column in place, non-destructively. We're still going to specify the full column definition, though, just like we do with add_column. There's also some index migration methods.

```
add_index(table, column, options)
```

```
remove_index(table, column)
```

Add_index and remove_index, we specify the table and the column which is going to be getting the index. We can make indexes across more than one column by passing in an array to column with all the column names in the array.

The options for add_index are different than the options for creating a column. The options for indexes are unique, either being true or false, where, whether it should be a unique index, and then name, which is where you can supply a custom name for this index. The Rails default is going to be to create a name based off of the column name, so you don't need to specify this unless you know you need something different. Let's try these out. Let's start by creating a new migration file. First make sure you're in the root of your Rails application, and then type rails, generate migration, and let's call this one AlterUsers.

```
rails migrate
```

We're going to be altering our users' table, so Alter is a good name for it. We're going to hit Return. You always want to give your migrations good common sense names, so it's clear what they're doing. So it created our new migration for us. Let's go over and open that up, take a look.

```
class AlterUsers < ActiveRecord::Migration[5.0]
  def change
  end
end

```

Here it is, AlterUsers, and it's got change in it. I'm going to make that, up, and I'm going to also add a new one down here for down. We'll come back and fill that out later. For now let's focus on up. So the first thing we're going to do is we're going to rename our table. Let's do rename, table and the old table name is users.

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

Let's change the name to be admin underscore users, and let's now add a column to our table, and the table name at this point is no longer users. It's going to be admin_users. So we want to make sure that we use admin_users here and not users, and then the column name that we want to add is going to be username. It's going to be a string, and it's going to have a limit of 25 characters. And I'm going to add another option here, which is after email.

Now there are a few special options, like after, which work with some databases, and after should work with my SQL but it does not necessarily work with all databases, so there's sometimes limited support for these. If a database doesn't support it, then it would be simply ignored. After we have our add_column, let's try one more, which is change_column, and we'll again target out admin_users table, and let's change the email, and let's make it a string, and we'll make it limit 100.

So I'm just going to change the length there and let's make another one, let's rename a column, we'll try a rename_column again on admin_users. And the column we want to rename is password, we're going to rename it as hashed_password. And, for the last thing, let's add an index, add_index on admin_users, and we're going to put it on the username column. You should add_index on all your foreign keys and also on any column which is used frequently for looking up records, like username.

An index is going to make those look-ups faster, just like the index of a back of a book does. Now, we need to write our down method to correspond to these changes. Remember the down method reverses the code in the up method, so the easiest way to make these changes is just to take these same steps, we'll paste them down here, and then we want to do them in reverse order and the reverse step. So I'll do add_index first, then we're going to do rename_column, then we're going to do change_column, and then, add_column followed by rename_table.

Now, we don't want to add the index now, we want to remove the index. We don't want to rename the column admin_users from password to hashed_password, we want to go the other way around. We want to rename it from hashed_password back to password. And we don't want to change column admin_users email string limit 100, we want to change that back to what it was, so we need to look that up. We need to figure out what that was, and that was, the default, which was just limit 255, right? So let's take that back.

Email, string and we'll make it no limit, but let's go ahead and specify also these defaults. Let's put those in there as well, just to make sure that those take place. And then, admin, add_column is going to be remove_column. We don't need any options for remove_column, we're just removing it. So we can just take that away, and then, rename_table from users to admin_users, also needs to be reversed. It's going to be admin_users changing back to users. Okay, so hopefully now you see how these are the mirror images of each other.

Not only have I reversed each of the operations, but I've done it in the reverse order. There's one last thing that I want to show you, and that is that we can output information to the console during the migration. So for example, let's put a little line here right before we add the index. I'm going to use puts, which is a Ruby method for output, and I'm going to just put three asterisks adding an index. And we'll just see what that looks like as well.

So let's try it all out now. Let's save our migration. Let's go back up to the command line and run
```
rails db:migrate
```

It will run all of our migrations, which should be only this one. The other ones should have already been run at the end of the last movie, and you can see it gives us output, tells us what it does.

```
== 20170323160008 AlterUsers: migrating =======================================
-- rename_table("users", "admin_user")
   -> 0.0276s
-- add_column("admin_user", "username", :string, {:limit=>25, :after=>"email"})
   -> 0.0429s
-- change_column("admin_user", "email", :string, {:limit=>100})
   -> 0.0329s
-- rename_column("admin_user", "password", "hashed_password")
   -> 0.0229s
*** Adding an index ***
-- add_index("admin_user", "username")
   -> 0.0215s
== 20170323160008 AlterUsers: migrated (0.1483s) ===================
```

 It's renaming the table, it's adding a column, and so on, and we can see my custom message here as well, Adding an index. Now we could go into my SQL and actually verify that these changes got made, but we can trust that Rails did make them for us. Now let's try migrating back down. You should always make sure that we can do the reverse migrations, that those work as well. So it's going to be rails DBmigrate, space, version, in all capitals, equals zero.
 ```
 rails db:migrate VERSION=0
 ```

We'll go all the way back down. This will undo all three of our migrations, and they all ran successfully.
```
== 20170323160008 AlterUsers: reverted (0.1384s) ==============================

== 20170323113242 CreateUsers: reverting ======================================
-- drop_table(:users)
   -> 0.0051s
== 20170323113242 CreateUsers: reverted (0.0052s) =============================

== 20170323111745 DoNothingYet: reverting =====================================
== 20170323111745 DoNothingYet: reverted (0.0000s) ==================
```

 And then I can migrate back up by just running rails Dbmigrate, and it should be able to do all three of them successfully back up again. So far, all of our migrations have run smoothly, and hopefully they ran smoothly for you too. In the next movie, we're going to talk about how to fix migrations when things don't go quite as we've planned.
