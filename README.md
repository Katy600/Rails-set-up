# Rails-set-up For MySQL

## First install MySQL
brew install mysql

rails new myapp

#### If you want to use MySQL
rails new myapp -d mysql

# Move into the application directory
cd myapp

#### That should install mysql to run on startup of your system. Test it with the following at the command prompt:

mysql -uroot

You should see the following:

Welcome to the MySQL monitor.  Commands end with ; or g.
Your MySQL connection id is 75
Server version: 5.5.15 Source distribution

Copyright (c) 2000, 2010, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or 'h' for help. Type 'c' to clear the current input statement.

mysql>

# Create the database
rake db:create

rails server
