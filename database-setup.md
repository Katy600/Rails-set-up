# Set up your database.
### In the terminal type...
Log in as root user, u space root, and then dash p for my password option. Type in your secret password. Now you are logged into mysql.
```
mysql -u root -p
```
Enter password - blank
### You should see.

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 5.7.17 Homebrew

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## Create your Development Database.
```
mysql> CREATE DATABASE name_development;
```

### You should see...
Query OK, 1 row affected (0.03 sec)

## Create your Testing Database.
```
mysql> CREATE DATABASE simple_cms_test;
```
### You should see...
Query OK, 1 row affected (0.00 sec)

### Define a new user that our Rails application can use to connect.
```
mysql> GRANT ALL PRIVILEGES ON simple_cms_development.* TO 'rails_users'@'localhost' IDENTIFIED BY 'password';
```
You should see...
```
Query OK, 0 rows affected, 1 warning (0.01 sec)
```

GRANT space ALL space PRIVILEGES, make sure you spell privileges correctly, space ON and then the name of the database. So the first one is simple_cms_development and then a period and an asterisk telling it that it should be all tables on that database. That's a wild card saying all tables on this database should be given access to and then in single quotes we'll write the name of the user.

It can be absolutely any name that you want. I'm gonna call it rails_user, so the rails_user, and then close those single quotes, put an at sign and then in single quotes write localhost, again single quotes. That's going to tell it that it'll be a locally connecting user, not someone connecting from another IP address somewhere else and then a space and IDENTIFIED BY and then in single quotes the password that we want to use. I'm just gonna use password.

 It'll now grant those privileges so that rails_user can now connect to all the tables on the simple_cms_development database.

### And now for the test version

```
GRANT ALL PRIVILEGES ON simple_cms_test.* TO 'rails_users'@'localhost' IDENTIFIED BY 'password';
```
You should see...
```
Query OK, 0 rows affected, 1 warning (0.01 sec)
```

##  Tell your Rails application about it.
```
exit
```
...out of SQL and go back over to your Rails app. Go to the database.yml which is inside your config file

This database file is in the yaml format. That's Y-A-M-L, it's short for yaml ain't mark up language, and really it's just a very simple way of formatting text files with indentions and colons.

- Change the user name to be rails_user
- Change the password password.

Once we have those installed, now our development and our test database can log in to mysql.

## Find out if the configuration worked.
Type in a Rails command...
```
  rails db:schema:dump
```
Now what this does is it says go out to the database and get all of the definitions for the database. Everything that describes what the database looks like and dump it to a text file.

### However, I got an error which said...

```
rails aborted!
Mysql2::Error: Access denied for user 'rails_user'@'localhost' (using password: YES)
```

### I was able to fix this by entering this...

```
GRANT ALL PRIVILEGES ON demo_project_development.* to rails_user@localhost IDENTIFIED BY 'password';
    FLUSH PRIVILEGES;
```

 If you look inside your db directory, you will see a new schema file has been created. There's nothing in there, it's empty, but it was able to connect to the database successfully. If you got an error, then you need to make sure that your database is running, that mysqul is running, that you've created those databases, and that you've granted privileges to the right user with the correct password and then provided those to the configuration. If all those things are true you should be able to connect to the database just like this.
