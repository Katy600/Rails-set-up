In the previous chapter, when we were creating, reading, updating, and deleting records, we were working with a *single model, in a single database table*, but what if we want to work with the data in related tables? After all, that's kind of the point of working with relational databases. We could query for a record, then read the value of its foreign key, and then make a second query using that foreign key in our query conditions to find the related record. That would be a valid approach, but it's a little tedious. **It would be better if we could define relationships between our models, and therefore between our tables, and then work with those relationships in an object oriented way**, and let Rails handle the behind the scenes work for us.

**ActiveRecord provides something called associations that allow us to define these relationships**. Let's start by talking about the different relationship types, and then learn how Rails handles each one. **There are three main relational database types, one-to-one, one-to-many, and many-to-many**. To help you visualize each one, let's start with a concrete example. Imagine a school. Each classroom in the school is assigned a teacher. The teachers don't change classrooms, and each teacher teaches four classes per day.

The students change classrooms throughout the day as they move from course to course. Okay, with that model in mind, now let's look at one-to-one associations.
# one-to-one
This is sometimes abbreviated as *1:1*, as a sort of shorthand. **An example of a one-to-one relationship would be the relationship between the classroom and the teacher**. There's ***one teacher that's assigned to each classroom***, so ***the classroom has one teacher***. Not more than one, only one. ***Each teacher belongs to a classroom***.

We would need to have a **foreign key** in order to maintain this relationship. *The foreign key would go on the teachers table*. If our other table is called the classrooms table, then the foreign key would called classroom underscore ID, and it would hold the ID, for room 107 in this case. You might wonder why we couldn't swap these two around, and assign the classroom to the teacher instead. You could. That's one of the things about one-to-one associations, it's a design choice as to which one is the parent, and which one is the child.

In that case, the foreign key would need to go in the classrooms table, then each teacher would have one classroom, and each classroom would belong to a teacher. To me, it makes a little bit more sense for the room to be a fixed resource, which has a teacher assigned to it. Teachers may come and go, but the room is still there. Again, it's a design choice. The important thing is to realize that *the foreign key goes on the belongs to side of the relationship.* Next, let's look at

# one-to-many associations.
These are sometimes abbreviated as 1:M.

An example of a one-to-many relationship is the relationship between each teacher, and the four courses that that teacher teaches. You would say that a teacher has many courses. In this case, Martha Cook has algebra, geometry, physics, and programming, and each one of those courses belongs to Martha Cook. Once again, we need a foreign key to maintain this relationship, and that foreign key is going to go on the belongs to side of the relationship, it would go on the courses table. Whenever ID the Martha Cook record has, let's say it's ID number 37, well then algebra would have a teacher ID of 37, to let us know that it belongs to Martha Cook.

# many-to-many association,
and you will see it abbreviated sometimes as M:N.
An example of a many-to-many relationship is the assignment between courses and the students who are taking those courses. Let's say that we have four courses, chemistry, geometry, physics, and programming, and we have four students, Michael, Jennifer, Jason, and Amy. Let's say that Michael is taking chemistry, geometry, and physics. Now, there's a little bit of a problem, because if we put a foreign key on the courses table to maintain the relationship between Michael and chemistry, then what would we do when Jennifer also wants to take chemistry? Chemistry's ID would already be pointing at Michael, so we couldn't do that, we would have to have many foreign keys, and if we have 100 students trying to take this course, we would need to have 100 foreign keys.

Is not a very good approach. We can't really put the foreign key on the other side either, with the students, because we can't say that Michael belongs to chemistry, because Michael also wants to take geometry and physics. We run into this problem where we can't really put the foreign key on either side. The solution is to use a join table that goes in the middle. The join table is what maintains these relationships. In the case of Michael, you can see that we have a join table link, that first green box, that links between chemistry and Michael.

It holds two foreign keys, one foreign key for chemistry, and one foreign key for Michael. Now, there's another one a few boxes down that maintains the relationship between Michael and geometry, and another one between Michael and physics. Each one of those has two foreign keys to hold that relationship in place. Now it's not a problem for Jennifer to also be assigned to the chemistry course, we just have to have another join table which has the chemistry ID, and Jennifer's ID. When we have a many-to-many association like this, we say that a course has many students, and belongs to many students, and on the reverse side, we say that a student has many courses, and belongs to many courses.

It goes both ways. **We would have two foreign keys, and we would put them in a join table**. Now that we understand all three relationship types, let's talk about the ActiveRecord associations that correspond, the way that Rails does these. It's no accident that I put quotes around the phrases has one, has many, and belongs to, because Rails is going to use those same terms as its method names.

## For a one-to-one relationship
```
Classroom has_one :teacher
Teacher belongs_to :classroom
```
you would say that a classroom class has one teacher, and the teacher class belongs to a classroom.

## For a one-to-many Association
```
Teacher has_many :courses
Course belongs_to :teacher
```
you would say that a teacher has many courses, and a course belongs to a teacher.

## for a many-to-many association
we have a very long method name called has and belongs to many, and that goes on both sides.
```
Course has_and_belongs_to_many :students
Students has_and_belongs_to_many :courses
```
Course has and belongs to many students. A student has and belongs to many courses. That's one way of saying it has many, and it belongs to many.

When you're writing in your Rails code, you'll need to write it out, and use the long name, but often, you'll find that developers use HABTM as a shorthand when they're discussing it. Now that we understand the different relationship types, and we've seen how ActiveRecord associations allow us to implement these types, let's try adding each one to our project, so that we can get a feel for them.
