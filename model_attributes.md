- [Instructor] In this movie, we will learn how model attributes work. If we were working with a pure Ruby class, we would need to define any attributes on the object using code something like this.

```
class AdminUser
  attr_accessor :first_name

  def last_name
    @last_name
  end

  def last_name=(value)
    @last_name = value
  end
end

```


I'm actually defining two attribute accessors here. I'm defining them two different ways. The first is the shorthand. I'm calling attr_accessor, and then :first_name to tell it that the name of the attribute should be first_name. That will allow me to read and write to the variable first_name on this class. Or I can write it out the longer way, which really shows what's going on.

I can have a reader, which is last_name and it just returns the value of the instance variable last_name. And I can have a writer method, which sets a value equal to last_name, so last_name equals some value. We'll set the instance variable last_name equal to a value. These do the same thing. So they're equivalent, first_name and last_name would behave the same. But I had to write them out, and that's very cumbersome, especially if we have a database where we have potentially 50 different columns.

```
class AdminUser < ApplicationRecord

end

admin = AdminUser.new
admin.first_name = 'Kevin'
admin.first_name
# 'Kevin'

```

In our Rails model, when we inherit from ActiveRecord::Base we gain a lot of behaviors and abilities that are built-in to ActiveRecord::Base. We already saw that one of those is that it knows where to locate the database table that corresponds to the model. It also knows what that database table looks like, and it knows what columns exist on the table. And it can use those columns to automatically provide us with attributes and the attribute methods that go with it. So we have a class AdminUser and it inherits from ActiveRecord::Base. It's going to look for a corresponding AdminUsers table, and if we have column for first_name on that table, then it will automatically give us attribute readers and writers for first_name.

And it does it for every single column that's on the database. Not only does it know that they are there, it also knows how to typecast them correctly so that values go into the database and come out of the database with the right type. So if it's a boolean, it goes in as a boolean value, and comes out as a boolean value. If it's an integer, it always goes in as an integer value, and so on.

 Now you can still define your own attribute accessors as well. For example, here I've defined one called :initials. And I can set and read values to it just like I would with any Ruby object.
```
 class AdminUser < ApplicationRecord
  attr_accessor :initials
 end

 admin = AdminUser.new
 admin.initials = 'KS'
 admin.initials
 # 'KS'
```

The difference though, is that :initials doesn't have a column in the database. So this data will not be saved the database, Ruby on Rails will not try and save it for us. And we won't be able to read it in the future. So it only exists inside the Ruby object as long as the Ruby object is around. It doesn't persist because we're not storing it in the database. So we get the best of both worlds. We get automatic attribute creation for anything that's in our database table, and we can define our own attributes just like we could in any Ruby class.
