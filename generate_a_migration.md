
```
rails generate migration MigrationName
```

 let's create our first migration. The way that we generate a migration is by using the rails generate command, which we saw earlier when we generated a controller. But this time we're going to do rails generate and then migration, followed by the name of the migration that we want to create, written in camelCase. You want to do this from the root of your Rails application so we'll type rails generate migration.

And let's call this first migration DoNothingYet. We'll hit return and it will create a migration file for us.
```
invoke  active_record
create    db/migrate/20170323111745_do_nothing_yet.rb
```

Notice that migrations reside in the *db directory*. That's because *that's the place to keep everything related to the database*. It also *creates a directory called migrate* for us, if it doesn't already exist. And then inside, *it added a new file*. It *converted our DoNothingYet in camelCase to underscore: do_nothing_yet.*

And then it *put the current date and time as a timestamp in front of it*. This timestamp is gonna be useful for a few reasons. If we create two migrations with the same name, we probably didn't create them at the exact same second. That can be especially helpful when we're collaborating on our project with other developers and we might both be creating migrations. *It makes each migration unique*. The timestamp also *puts our migrations in order, sorted by the time* that they were created. We usually want to make changes to the database in a particular order.

For example, we want to run a migration that creates a table before we try to run a migration that adds a new column to that table. We can also look in our migrate directory and find the newest migrations by looking at the latest dates. Migrations also use this timestamp as an identifier. We're going to refer to a migration by its timestamp identifier, not by its name. Let's open up the migration file that it created for us and see what's there. Inside the db directory, inside migrate, we'll find that file.
```
class DoNothingYet < ActiveRecord::Migration[5.0]
  def change
  end
end

```
And that file has a Ruby class defined in it. You can see our camelCase name DoNothingYet there. *It inherits from active record migration*. That's what gives it all of its abilities to behave like a migration, is the fact that it inherits this behavior from this Rails class. And then at the end it tells it what version of Rails we were using when we created this migration, just in case that makes a difference. *Inside the class is one method called change*. Change is a shorthand for having separate up and down methods.

```
class DoNothingYet < ActiveRecord::Migration[5.0]
  def up
  end

  def down
  end
end

```

When we run our migrations, Rails is going execute the code that's inside our up or our down methods. *Up* will **describe the changes we want to make to the current database**. **Down** will **describe how to reverse those changes**, to undo the code that's in the up method. They're gonna be *mirror images of each other*. If the *up method creates a new database table*, then we would expect the *down method would delete or drop that table*.

The *change method* is a *shorthand which allows us to write only the up code, and then let Rails try to reverse those actions*. For example if we create a new database table, Rails is smart enough to realize that the reverse of creating a table is dropping that same table. Not all actions can be automatically reversed though. In which case, you can't use change. You would have to use these explicit up and down methods.

Now this is just a sample migration so I'm not going to fill these methods out yet. If we tried to run this migration now, it would execute the code in the up method, but it wouldn't make any changes to the database because there's no code there to make changes. I wanted to show you the most basic way to generated a migration first. Now in the next movie, I want us to see another way to create migrations by generating a model.
