- [Teacher] In this movie, we will learn how to use named scopes in our models. So far in this chapter, we've learned to create, update, delete, and find records, and we did it without adding any additional code to our model. We've been using the built-in methods provided by ActiveRecord and ActiveRelation. Rails gives us these features for free, simply by defining a model so that it inherits from ActiveRecord::Base, which most of our models will. You can also write your own query methods, which can use and combine the built-in query methods. We can design a query and then save it in our models as a named scope.

## Named scopes
**assign a name to an ActiveRelation query and then store them in a model**. We can *call named scopes just like we call ActiveRelation query methods*, and we can even *daisy-chain them together with other ActiveRelation query methods*. And they can *accept parameters*, just like a method would. In Rails 5, we're gonna make sure that *we define these named scopes using something called the lambda syntax*. Let me demonstrate.
```
scope :active, lambda {where(:active => true)}
```

Inside our model, you would just **add a line to define the scope that you want**.

So here, for example, I have a **named scope**, and I *start out with the word scope, and then I give it a name*. *That name is gonna be active*, that's the name I'm gonna call this scope by. And then after that, I've got the **lambda syntax**. I've got the **literal word lambda**, and then I've got **curly braces**, and inside the curly braces, you'll see something familiar. You'll see one of those **query methods** that we've just been looking at *where active equals true*. ***So now, when I call the scope active, it's going to perform this query method, where active is true***.

## There's another way that you can write the lambda syntax,
```
scope :active, -> {where(:active => true)}
```

and that is with the dash and the greater than sign. It looks like an arrow. **It's also called the stabby lambda**, and some people prefer it over using the actual word lambda. I happen to prefer using the word lambda, so that's what I'm gonna stick with.

## This is the same thing as if we had a ***method that was defined on the class that was a class method***.
```
def self.active
  where(:active => true)
end
```
The **self lets us know that it's a class method**, and then ***inside*** it, *we could have that same scope*. These work exactly the same way.

We've named them **scope in one place** and we've named them **method in the other**. Most of the time, if we're working with query methods, the Rails convention is to make them into scopes and not methods.
```
Customer.active
```
And then when we wanna call a scope, all we have to do is take the class and then put the scope name after it, and it will do whatever is inside that scope.

## Scopes can also take arguments.
```
scope :with_content_type, lambda {|ctype|
  where(:content_type => ctype)
}
```
The way that we do that is inside that lambda syntax, you need to add upright pipes, that's the two lines with the variable name that you want to allow to be passed in.

So in this case, **it's expecting that when we use the scope with_content_type, that it will also have a variable passed in that is going to be assigned to ctype**, and then you can see that it's doing where content_type is equal to whatever that variable happens to be.
## It's the same thing as if we wrote out a method like this:
```
def self.with_content_type(ctype)
  where(:content_type => ctype)
end
```

self.with_content_type, and then we had an argument to that. Both of them work the same way, and would get called in the exact same way. So why do we need to use this lambda syntax? Why can't we just call that where directly?
```
Section.with_content_type('html')
```
In fact, you used to be able to do it that way in Rails.

But now, the lambda syntax is required, and the reason why is because there were a lot of problems about when these definitions were being evaluated.
```
# Evaluated when called, not when defined.
scope :recent, lambda {
  where(:created_at => 1.week.ago..Time.now)
}
```

When we use lambda, **it's going to be evaluated when it's called**. *At the time that we call the scope is the time at which that statement is going to be evaluated*, **not** at *the time in which it's defined*. ***The time that it's defined is when Rails first boots up***, when *Rails first launches* and *gets the class and grabs it and gets ready to go*. That could potentially be days or weeks before we actually trigger the method.

So it's important to know when this code is gonna be evaluated. Is it evaluated when the model is first loaded, or is it defined when it's called? That makes a big difference, especially if we're talking about time. For example, let's say that I had a scope called recent, and I wanted to find everything created from 1.week.ago to Time.now. Well Time.now and 1.week.ago really depend on when I'm calling it. When I say Time.now, I'm expecting it to actually be now, the time that I'm calling it, not the time that this file was first loaded in.

So that's why it's important to use that **lambda syntax**. And as I mentioned earlier, you can **chain scopes together**, and this can give you some nice syntax for understanding what's going on in your code. For example, I could have...
```
Article.recent.visible.newest_first
```

 Now that, behind the scenes, could just be calling regular Rails query methods, but it's much shorter, and it's much clearer what it's doing. I'm asking for recent visible articles with the newest one first.

## Let's try adding some scopes to our subject model.
So inside your app directory, inside models, we wanna open up subject, and inside this Subject class,

```
class Subject < ApplicationRecord

end
```

we're going to add our first bit of code, and that's gonna be a scope.

```
class Subject < ApplicationRecord

  scope :visible, lambda { where(:visible => true) }

end

```
So we start with the word scope, and then **we wanna give the scope a name. Let's call this first one visible**. Then we put a comma, and then that **lambda syntax**, lambda, and then curly braces. Now *inside here is where we'll say what visible is*. So what's the definition of a visible subject? Well, **it's where visible is true**. Now I could've just used this, but visible is much shorter. It's a little clearer what it does, and in some cases, this might not be so simple. There might be a lot more behind it.

Let's add another one.
```
class Subject < ApplicationRecord

  scope :visible, lambda { where(:visible => true) }
  scope :invisible, lambda { where(:visible => false) }

end
```

let's make one that is called invisible, and that's gonna be visible false. See how that works? Now I can call, ask for visible subjects, or I can ask for invisible subjects, and it will return the right thing to me. One that I like to have on a lot of my models is sorted. So what is my sort gonna be? If I just ask for it to be sorted, what is my default sort? I'm gonna say this one is order, and let's make it position ascending.
```
class Subject < ApplicationRecord

  scope :visible, lambda { where(:visible => true) }
  scope :invisible, lambda { where(:visible => false) }
  scope :sorted, lambda { order("position ASC")}
  scope :newest_first, lambda { order("created_at DESC")}
  scope :search, lambda {|query| where(["name LIKE ?", "%#{query}%"])}
end
```

And then let's add another one, scope, newest_first. Again, that's gonna take a lambda, and we'll make this one order, this one's gonna order by created_at descending. That'll put the newest one first. And let's make another one that is gonna be for searching, and that way we can see an example of an argument. Lambda, and then inside, we need to have our query string, where, and then I'm gonna use the array syntax, name LIKE, question mark, and then comma, and then the string to drop in place is going to have percent signs on either side of it, which will indicate to SQL that it should use wildcard matching.

And then I'll interpolate the query, so the query will get dropped in here, and then it will be safely placed into this spot right here. Because this is dynamic data, it might be coming from a user, I wanna make sure that I use the question mark form in order to sanitize it. Alright, and let's hit Return there. Now that I've got my scopes defined, let's save it and let's go back over and


## let's try it out. My rails console, and let's try asking...
```
Subject.visible
```

```
  Subject Load (0.4ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```

I get my visible subjects, Subject 1 and Subject 2.

## Let's try invisible,
```
2.2.3 :002 > Subject.invisible
  Subject Load (0.4ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 0
 => #<ActiveRecord::Relation [#<Subject id: 4, name: "Third Subject", position: nil, visible: false, created_at: "2017-03-27 08:22:03", updated_at: "2017-03-27 08:22:03">]>
2.2.3 :003 >
```
I get back Subject id: 4. That's the one where visible is equal to false.

## Let's go back and try visible again, and this time let's tell it to sort them...
```
Subject.visible.sorted
```
```
Subject Load (2.2ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1 ORDER BY position ASC
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">, #<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">]>
```

Now I get them sorted.

## Let's try sorted by newest first,
```
Subject.visible.newest_first
```
```
  Subject Load (12.2ms)  SELECT `subjects`.* FROM `subjects` WHERE `subjects`.`visible` = 1 ORDER BY created_at DESC
 => #<ActiveRecord::Relation [#<Subject id: 2, name: "Next Subject", position: 2, visible: true, created_at: "2017-03-24 15:38:33", updated_at: "2017-03-27 07:45:43">, #<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">]>
```

 and now you see that we get id: 2 comes first.

## And then let's try a search.
```
Subject.search("Initial")
```
```
  Subject Load (31.2ms)  SELECT `subjects`.* FROM `subjects` WHERE (name LIKE '%Initial%')
 => #<ActiveRecord::Relation [#<Subject id: 1, name: "Initial Subject", position: 1, visible: true, created_at: "2017-03-24 15:25:18", updated_at: "2017-03-27 07:40:21">]>
```
There it is, it returned Initial Subject. So just by telling search, it searched the name column because that's what I asked it to do. Search for a name LIKE Initial, and that meant it didn't have to be an exact match, it could be a partial match as well.

***So that's how you create named scopes. They're a good way to take lots of complicated logic and store it in your model with a simple label that really indicates what your intent is when you call it.
