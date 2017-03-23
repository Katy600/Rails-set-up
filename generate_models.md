#learn to generate models along with their migrations.

The way that we're going to do that is using the *Rails generate command*, but this time, we're going to specify that we want to generate a model, and we're going to provide the name of the model in Camel case.

```
rails generate model ModelName
```

Just like we did with the migration, but this time, we're telling Rails that it's a model, not just a migration on its own. To demonstrate, we're going to create the user model that we're going to use in our simple CMS application. The way that we will do that is with Rails generate model, and then, in Camel case, the name of the model we want to create, and it's going to be singular, it's very important.

```
katymccann (master *) simple_cms $ rails generate model User
Running via Spring preloader in process 36961
Expected string default value for '--jbuilder'; got true (boolean)
      invoke  active_record
      create    db/migrate/20170323113242_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml

```

It's going to create a few files for us. Notice that it created a migration for us, it gave it a *timestamp*, and you will notice that the timestamp is different than the timestamp we used previously, and it *named it for us*, *create underscore users*. Notice that it's *plural* there. That's because *the model name is going to be singular, but the table name is going to be plural*, that's the *Rails convention*. The model name is singular, it defines what a singular item looks like, while the table name is going to be plural, because it's going to contain many rows of that item.

You can see that *it also created a new file for us, in app, models, user dot RB,* that's where we will put the definitions for a model later on, and it gave us some placeholder files in our test directory for testing the model. Let's go and look inside the migration it created.

```
class CreateUsers < ActiveRecord::Migration[5.0]
  def change
    create_table :users do |t|

      t.timestamps
    end
  end
end
```

Inside, you can see that it named *it create users*. Again, it *inherited from ActiveRecord migration*, and it gave us the *shorthand change definition*, so we now know what this does, it's a short hand. *Rails knows, since we are creating a table, that the reverse of that would be to drop the table*.

Let's make it explicit though,

```
class CreateUsers < ActiveRecord::Migration[5.0]
  def up
    create_table :users do |t|

      t.timestamps
    end
  end

  def down
    drop_table :users
  end
end
```
so let's change this to be *up*, and we know to do here, we just need to add a new line, which is drop underscore table, and then the table name. The table name can actually be a symbol or a string, it doesn't make a difference. The important thing is that this one creates the table, and that this one drops the table. What isn't included yet are the column definitions for this table. It's just given us a mostly empty code block, that's everything from do down to end. We'll come back and talk about what this does a little bit later on.
```
class CreateUsers < ActiveRecord::Migration[5.0]
  def up
    create_table :users do |t|
      t.column "first_name", :string
      t.string "last_name"

      t.timestamps
    end
  end

  def down
    drop_table :users
  end
end
```
The important thing to know is that inside this code block is where we want to put our column definitions, and we don't have any yet. What it does is it *yields up the table as the variable T,* and then *we apply columns to that table*. For example, the *first column definition might be T dot column*, and then the *name of the column*, let's make it first name, and *then the type of column that we want to create*, I'm going to call it string. Now, with MySQL, string is going to translate to be a varchar column.

If we were using a different database, then Rails would translate string into whatever type that database used. Migrations try to be database independent. That's one of their strengths. That's one of the nice things about them, is that we can write in the Rails migration code string, and Rails will take care of translating it to whatever the database needs. Now, there's a shorthand that you can use for writing out these column definitions as well. Instead of having to write column, since we know that pretty much *everything we're going to do inside this code block is going to be add columns, we can just simply write T dot string, and then put the name of the column that we want*.

For example, *T dot string last name*. Now, these aren't exactly equivalent, because the name of the column is going to be different, one's first name, and one's last name, but they do work the same way. This is just a *shorthand syntax for creating a column with the string type*. The shorter format is probably the one that you will see the most often, however, you should be familiar with the longer format too, because the add column syntax, which we're going to see soon, doesn't have a shorter version, so you should be comfortable with both of them. Now, string is one type of table column that we can have.

What are the other tapes that we can use? Well, there's *binary, Boolean, date, datetime, decimal, float, integer, string, text, and time*. *Each one gets translated into the appropriate column type for the MySQL database*. *Integer becomes int, string becomes varchar, text becomes text*, etc. The column types you're going to use most often are string, integer, and Boolean, and after that probably comes datetime, decimal, and text, but it really depends on your application, and what you are use case is.

In addition to defining the column type, we can *pass in some options for the columns*. We can *specify limit*, that is the size of the field, or *how many characters it will accept*, or *how large an integer can be*, whether there should be a *default value set*, whether or not the column is allowed to be null, that is to have no value at all, and for the decimal type, we can specify the decimal precision and scale. Rails uses sensible defaults for all of these values, so you only need to specify them when you know that you need something different.

```
class CreateUsers < ActiveRecord::Migration[5.0]
  def up
    create_table :users do |t|
      t.column "first_name", :string, :limit => 25
      t.string "last_name", :limit => 25
      t.string "email", :default => '', :null => false
      t.string "password", :limit => 40
      t.datetime "created_at"
      t.datetime "updated_at"

      t.timestamps
    end
  end

  def down
    drop_table :users
  end
end



```

Let's add an option for our first name, and let's specify that it should have a limit of 25. For our last name, let's add an option that its limit should be 50. Let's add another column after this. Let's make one for email, T dot string, email, and let's give it a default value of nothing, and say that it can't be null. Null false. Since we had a default value of nothing, that means that someone would have to try to set it to be nothing for that to be a problem.

Let's make another one, T dot string, and let's make this one password, and we will give it a limit of 40. Now, if you don't limit these in MySQL, they're going to turn out to be 255 characters, and there's nothing wrong with using those defaults, I just want to show you how to limit them if you need to, but there's nothing wrong with just letting those go to their natural 255 limit. There's two more that we could add here that are very common, and very helpful, T dot datetime, and we're going to call it created at, and another one, T datetime, we're going to call this updated at.

These are special columns that include a bit of magic. If a column has one of those two names, then Rails is automatically going to populate and update them for us. It's very handy to have these two values on almost every single table. You'll know when a record was created, and you'll know when it was last updated, and you don't have to do any additional work, Rails is going to manage those values for you. Because they are used so often, they also have a shorthand, which is T dot timestamps. You can see that Rails gave that to you by default, so we don't actually need these, I can comment them out, and I'm just going to move them below timestamps, so you can see that's what this is doing.

T dot timestamps is the same thing as adding created at, and updated at. Now that I've written our code for creating our first table, you may have noticed that I don't anywhere have a column for ID. Almost every table that we create is going to have an ID, a unique, auto incrementing integer that identifies the record. It's also referred to as the primary key. Having an ID column as a primary keys on tables is so common that Rails will automatically add it for us. That's Rails's sensible defaults at work.

We don't have to remember to add a column for ID, Rails will do it. In fact, we only need to specify when we don't want it to add an ID column. In that case, after create table, we would add an argument that says ID false,

```
class CreateUsers < ActiveRecord::Migration[5.0]
  def up
    create_table :users, :id => false do |t|
      t.column "first_name", :string, :limit => 25
      t.string "last_name", :limit => 25
      t.string "email", :default => '', :null => false
      t.string "password", :limit => 40

      t.timestamps
      # t.datetime "created_at"
      # t.datetime "updated_at"
    end
  end

  def down
    drop_table :users
  end
end

```

and that would turn off that behavior for us. 
