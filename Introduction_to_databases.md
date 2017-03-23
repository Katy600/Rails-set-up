# The fundamentals of working with databases.


With a spreadsheet we have it all laid out in front of us and a visual medium where we can see those tables with their rows and columns. In databases we don't have that. We're going to have to be working with subsets of the data all the time. And we can ask the database to show us the information we want. We're going to be issuing commands and it's going to return data back to us that we can then work with. And perhaps, it goes without saying, that spreadsheets are optimized for adding numbers. That's what they do best. Databases are optimized for working with data and the relationships between data.

Rails is going to provide a very friendly layer over SQL so that you don't have to write SQL very often.

### There's a special vocabulary that's associated with databases.

we have a *database*, which is *a simple set of tables*. A database is a collection of database tables. In Rails, one application typically equals one database. It doesn't have to be true, but at the beginner and most basic level, it is true.

The database might be named simple cms development. The name is typically all lower-case with underscores, which is the convention in Rails. Databases are the level where we *grant access permissions*. We won't have specific table permissions so that some people can view some tables, but not others. We're gonna give our Rails application permission to access the entire database, and then we'll let the application code decide whether a user ought to be able to access certain information and certain tables, or not.

A table is a set of rows and columns. In the Rails framework, one model equals one table. *The model and the table represent a single concept*. A noun, the what of our application. For example, in this simple CMS that we build, *the nouns might be users, subjects, or pages*. These table names also are all lower-case with underscores, and they're plural, because the table will contain many of this type of item.

*We have many subjects in a subjects table, so we pluralize it*. Tables are where relationships are going to be defined.The next term we have is column. *A column is going to be a set of data of a single simple type*. In the Rails framework, *a single attribute of a model is equal to a column*. For example, first name, last name, email, and password, would all be attributes of our user model.

And they would all be columns in our table, as well. And when I say that a column is a set of data of a single simple type, what I'm talking about are *data types like strings, integers, dates*. A table is made up of columns and rows. *The rows are the data*. *Each row is equal to an object or an instance*. In the case of our users, a row would be all of the data about one user. So, we might have, Kevin, Skoglund, an email address, and a password that correspond to each of those columns; first name, last name, email, and password.

Next, we have *field*, which is an *intersection of a row and a column*. It has a *single value*. An example might be the first name field. We have the value Kevin. Often field is used interchangeably with column. So don't be surprised if you hear me say field instead of column, or column instead of field. Next, we have *index*. An index is a *data structure on a table to increase the look-up speed of data inside that table*. It's like the index at the back of a book. *It lets you find rows in the database very quickly*.

Next, we have *foreign keys*. Foreign keys are an important concept when working with relational databases. A foreign key is a *table column whose values reference rows in another table*. They relate rows in one table to another through this reference. These relationships that foreign keys provide is why we call them relational databases. An example might be if we had a pages table. We would have a subject ID in that table, which would reference the ID of the subject table.

Notice that it uses a singular foreign table name. We don't say subject's ID, we say subject ID. It corresponds to our subjects table and then we put ID at the end. Again, this is the Rails convention and it can be overwritten. If you'd worked with databases before this is going to be a familiar concept. If not, don't worry. We're going to come back to it in greater detail later. *You do always want to remember to put indexes on your foreign keys*. Remember, *indexes help you look up information quickly* so this will give us access to those relationships as fast as possible.

Next, we have *schema*. The schema is going to be the *structural definition of a database*. *It defines the tables, the columns, the indexes*, everything that makes up a database. So, when I talk about the database schema that's what I'm referring to. And the last term that I want to introduce you to is *CRUD*. This isn't specifically a database term, but you're going to hear me saying it a lot in upcoming chapters. And it's an acronym for *Create, Read, Update, and Delete*. These are the four main ways that we're going to interact with the database.

*We create new rows of data, we read existing rows of data, we update existing rows with new data, and we delete rows of data*. Those are the four main things that our Rails application is going to be doing with databases.
