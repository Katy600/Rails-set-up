# There are four useful SQL commands that you should learn.

###Show databases,
```
SHOW DATABASES;
```
which shows a list of all the databases inside my SQL.
###Create database
```
CREATE DATABASE db_name;
```
followed by the name of the database we want to create.
###Use,
```
USE db_name;
```
and then the name of the database we want to use. That's especially useful for switching between databases.

###drop database,
```
DROP DATABASE db_name;
```
with the name of the database we want to drop or completely remove. And that removes the database and all tables associated with it.


It's a best practice not to connect to our database as the root database user. It's much better to create a new user and give them permission to access the database.

```
GRANT ALL PRIVLEGES ON db_name.*
TO 'username'@'localhost'
IDENTIFIED BY 'password';
SHOW GRANTS FOR 'username'@'localhost';
```

The way we do that, is by using *grant all privileges*.

You call *grant all privileges*, you *say what database*, you *specify the user*, *where they're connecting from*, and the *password* that they would use. Notice the *period and asterisk after the database name*. That's a *wild card and it means all tables that are on that database*. So this user would have access to all tables on this database. Now, if this is not for a pre-existing user, we're both creating the user and granting them privileges all in one step. This command is wrapped around three lines.

But you can type it all as one line or hit return, it doesn't matter. The semicolon at the end is what lets my SQL know that you're done typing. The second command, show grants, is just a good way to check and see what privileges have been added for this user in the past. Once that database is created, and the user has been granted access to it, we need to *configure our Rails project* to connect that database as that user. The database configuration is located in the config directory,
```
config/database.yml
```
 in the database.yml file. Once you add the database name, the user, and the password to that file, Rails will then have access to the database.

A good way to confirm that Rails does have access is to dump the database schema file.
```
rails db:schema:dump
```
 And we saw how you could do that using rails db:schema:dump. Once we have a database created and configured, we're ready to tell Rails how to add tables to the database.
