
 Now that we've written the create users migration, we need to run the migrations in order for those changes to take effect on our database.

```
rails db:migrate
```

 The way that we run a migration is from the command line, again using the rails command, but this time with bd:migrate. db is gonna specify one of the many database tasks that we could use and then migrate is the specific task that we want to perform. So, make sure that you are still in the root of your rails project and from there type rails db:migrate Now, this used to be called rake db:migrate, so if you're familiar at all with previous versions of rails, this used to be called rake.

It's been changed in version 5 to be rails db:migrate, and you want to use that here. We'll hit return. By default, rails db:migrate is gonna be targeting our development database. That's where all the changes are gonna take place. So, now the migration ran, and it gave us some output.
```
katymccann (master *) simple_cms $ rails db:migrate
== 20170323111745 DoNothingYet: migrating =====================================
== 20170323111745 DoNothingYet: migrated (0.0000s) ============================

== 20170323113242 CreateUsers: migrating ======================================
-- create_table(:users)
   -> 0.0243s
== 20170323113242 CreateUsers: migrated (0.0244s) ==========================
```

And, this is the typical output that you'll see when a migration runs. You'll see that it tells us the time stamp and the name of the migration it's running. It will tell you that it's beginning the migration. It will tell you that it finished the migration. With DoNothingYet, it didn't do anything inside of there.

For create users, it said migrating, and then it told us that it was creating the table, users, and then it told us when it was done, and it also tells you how long the process took. Now, you'll notice that when we ran db:migrate, it ran both migrations. When we run db:migrate, by default it runs all migrations which have not yet been run. We'll see how it keeps track of those in just a moment. Let's go into MySQL and take a look at the changes that it made.

```
mysql -u rails_user -p simple_cms_development
```

I'll put the database name here, so I don't have to switch to it. It'll put me directly in there. The -p option tells it that I should enter my password. I'm gonna do that now. And now, I'm logged into MySQL.

```
katymccann (master *) simple_cms $ mysql -u rails_user -p simple_cms_developmentEnter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 58
Server version: 5.7.17 Homebrew

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Alright, let's begin by doing SHOW TABLES This will show me all of the tables that I have on simple_cms_development,

```
mysql> SHOW TABLES;
+----------------------------------+
| Tables_in_simple_cms_development |
+----------------------------------+
| ar_internal_metadata             |
| schema_migrations                |
| users                            |
+----------------------------------+
3 rows in set (0.01 sec)

```

 and you can see that there's internal_metadata, we don't need to worry about that, there's schema_migrations, which we'll look at in just a moment, and then there's users. users is the table that we just created.

Let's look at SHOW FIELDS FROM users;

```
mysql> SHOW FIELDS FROM users;
+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int(11)      | NO   | PRI | NULL    | auto_increment |
| first_name | varchar(25)  | YES  |     | NULL    |                |
| last_name  | varchar(25)  | YES  |     | NULL    |                |
| email      | varchar(255) | NO   |     |         |                |
| password   | varchar(40)  | YES  |     | NULL    |                |
| created_at | datetime     | NO   |     | NULL    |                |
| updated_at | datetime     | NO   |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
7 rows in set (0.00 sec)

mysql>
```

and you'll see that it gives us a list of the different fields that it's created, the different columns and their definitions and you can see that we have integers, we have varchars. It set the limits on those, varchar(25), varchar(50), email was allowed to go to its default which is 255, and password we set to 40. These can be NULL, but email can't be NULL because we set that and we gave it a default value of being blank.

Again, notice it did create the id for us. id was automatically created. It's an integer of type 11. It can't be NULL. It's a primary key, and it auto increments. And that is what you would ordinarily want as default values for the id. And then you can see those created_at and updated_at time stamps, that it created automatically for you. Let's take a look at this other table here, schema_migrations So, I'm going to
SHOW FIELDS FROM schema_migrations;
```
mysql> SHOW FIELDS FROM schema_migrations;
+---------+--------------+------+-----+---------+-------+
| Field   | Type         | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| version | varchar(255) | NO   | PRI | NULL    |       |
+---------+--------------+------+-----+---------+-------+
1 row in set (0.00 sec)

```

You can see that there's just one column in here and it's called version, it's a string of type 255.

Let's see what's inside there. SELECT * FROM schema_migrations;

```
mysql> SELECT * FROM schema_migrations;
+----------------+
| version        |
+----------------+
| 20170323111745 |
| 20170323113242 |
+----------------+
2 rows in set (0.00 sec)

```
You can see that *it stored the two time stamps for the migrations that it just ran.* So, this was the first one, the DoNothingYet, and this was our create users time stamp. *This is how rails keeps track of which migrations have been run already*, by adding an entry to this table. If we run seven migrations, and then we add another three migrations rails will know which three migrations need to be run, by comparing those file names, the identifier that's there, against the version number that's here.

Alright, let's exit out of MySQL, and let's also just go over into our application, and let's take a look in the database directory in db directory at schema.rb Remember, we dumped this file earlier. I just want you to see that when we run our migrations, it also updates the schema, as well. It tells us what version it is, again that's that time stamp of the latest migration that got ran. You can see that it ends in 45 just like our last migration ends in 45.

```
ActiveRecord::Schema.define(version: 20170323113242) do

  create_table "users", force: :cascade, options: "ENGINE=InnoDB DEFAULT CHARSET=utf8" do |t|
    t.string   "first_name", limit: 25
    t.string   "last_name",  limit: 25
    t.string   "email",                 default: "", null: false
    t.string   "password",   limit: 40
    t.datetime "created_at",                         null: false
    t.datetime "updated_at",                         null: false
  end

end
```

And you can see then, it has the definition of our database. So the schema file should always contain all of the definitions defining the current database in its current state, this is what it looks like and these are the steps that it would take to recreate that database. So we ran both of our up methods, when we executed those migrations. What about migrating back down? The way that we do that is with rails bd:migrate and then we specify VERSION=0 and VERSION must be in all capitals, it's very important, so VERSION=0 will set this variable equal to 0, and now when we run rails db:migrate, it undoes all of those migrations to go back to version 0.

```
rails db:migrate VERSION=0
```

You remember that our schema file keeps track of the version. you can see it changed now to version 0.

```
ActiveRecord::Schema.define(version: 0) do

end
```
 That's because we undid these, right.

```
== 20170323113242 CreateUsers: reverting ======================================
-- drop_table(:users)
   -> 0.0349s
== 20170323113242 CreateUsers: reverted (0.0350s) =============================

== 20170323111745 DoNothingYet: reverting =====================================
== 20170323111745 DoNothingYet: reverted (0.0256s) ===========================
```
 CreateUsers was reverted. DoNothingYet was reverted. Notice that it did the migrations in reverse order. That makes sense. If we did 10 steps forward, then we've got to do those 10 steps backwards, in reverse order. And if we go back into MySQL again, and let's take a look at our schema migrations file you can see that it's empty.
 ```
 mysql> SELECT * FROM schema_migrations;
Empty set (0.00 sec)
 ```

And if we take a look, lets do SHOW TABLES,
```
mysql> SHOW TABLES;
+----------------------------------+
| Tables_in_simple_cms_development |
+----------------------------------+
| ar_internal_metadata             |
| schema_migrations                |
+----------------------------------+
2 rows in set (0.00 sec)
```

you'll see that that's the only file that's there, our users table is now gone. Okay, so we've seen how to migrate up and we've seen how to migrate back down. What about migrating only part of the way? One helpful tool for knowing where the migration is, is
```
rails db:migrate:status
```
And this will go in and have rails report to you which ones of the migrations have been run and which ones have not been run. So, you can see at the moment, this first Migration ID is down.
```
katymccann (master *) simple_cms $ rails db:migrate:status

database: simple_cms_development

 Status   Migration ID    Migration Name
--------------------------------------------------
  down    20170323111745  Do nothing yet
  down    20170323113242  Create users
```

It has not been run. It is not currently there. This is also a good way to get the ID numbers. You can copy and paste this ID number so we can have rials db:migrate and this time let's specify the VERSION= and we'll paste in that first identifier. So, guess what. Now it runs all of the migrations to get up to that version. So, if we had 10 and we picked the fifth one, it would run the first five migrations. It doesn't just run that one migration only.

```
katymccann (master *) simple_cms $ rails db:migrate VERSION=20170323111745
== 20170323111745 DoNothingYet: migrating =====================================
== 20170323111745 DoNothingYet: migrated (0.0000s) ===========================
```

It runs everything it needs to get to VERSION 2016072162630. Now, if we again call rails db:migrate:status, it'll come back and it'll tell us that this one has been run.

```
katymccann (master *) simple_cms $ rails db:migrate:status

database: simple_cms_development

 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20170323111745  Do nothing yet
  down    20170323113242  Create users
```

 Where as, this one still has not been run.


 So, for running migrations,
```
rails db:migrate
```
```
rails bd:migrate VERSION=0
```
```
rails db:migrate VERSION=20170323111745
```
```
rails db:migrate:status
```
```
rails db:migrate:up VERSION=20170323111745
```
where you specify a particular version
```
rails db:migrate:down VERSION=20170323111745
```
it doesn't go to that version, it only runs the up method on that single migration, and down does the same thing, so you should always provide a version number when you're using up or down, because you're telling it specifically go run that migration's up or down method
```
rails db:migrate:redo VERSION=20170323111745
```

 redo is the same thing as doing down for that migration, and then doing the up again. By default, it uses the last version, so it can be helpful if you're only running one migration, you can just say redo and it will do a down and a quick back up again.
