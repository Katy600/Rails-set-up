# Set up your database.
### In the terminal type.
mysql -u root -p

Enter password - blank
### You should see.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 5.7.17 Homebrew

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>

## Create your Development Database.
mysql> CREATE DATABASE name_development;

### You should see...
Query OK, 1 row affected (0.03 sec)

## Create your Testing Database.
mysql> CREATE DATABASE simple_cms_test;

### You should see...
Query OK, 1 row affected (0.00 sec)
