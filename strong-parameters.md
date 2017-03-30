
# Mass Assignment and Strong Parameters

In the last movie, we created a web form which will submit its values to the create action but, when we tried to use it, we got back an error. To fix that error, we need to learn about **mass assignment and strong parameters**.
#### Mass assignment
- is the term for **passing a hash of values to an object to be assigned to the object's attributes**.
```
Subject.new(params[:subject])
Subject.create(params[:subject])
@ubject.update_attributes(params[:subject])
```
New, create, and update_attributes are the primary methods that use mass assignment but there are a few others, as well. In each one of these cases, you can see that **we're taking a hash of values and we're essentially just dumping them into the object and asking the object to assign all of the attributes based on that hash**.

That's what mass assignment is. Rails is making our lives much easier by allowing us to assign values to this object all at once instead of having to do it on-by-one.
```
subject = Subject.new({:name => "Test", :position => 5, :visible => true})

subject.name = "Test"
subject.position = 5
subject.visible = true
```

**Unfortunately, this convenience also introduces a major security issue**. Imagine that we have a form which lets users update their username.
```
@user.update_attributes({:username => "kmccann"})
```
It's a parameter for username and we use update_attributes, which then uses mass assignment to set that value. Now, imagine that a hacker figures out a way to submit a fake version of the form and, on the fake form, the hacker includes some extra fields which then show up in our form parameters.

```
@user.update_attributes({}:username => "kmccann", :password => "notsecret", :admin => true})
```

Rails dutifully grabs all those attributes and mass assigns them to the object before updating the database. Our imaginary hacker was just able to gain access and admin privileges to our application.

## Mass Assignment Filtering
Instead, we need a way that we can filter the attributes which are allowed to be mass assigned to prevent this from happening. Rails has evolved in how this filtering is accomplished. In early versions of Rails, attributes could be declared as protected, or disallowed from mass assignment, in the model.
```
Rails v1-2: blacklisting of attributes
```

In programming, when everything is available by default, and we list only the items that are disallowed, it's called blacklisting.

It was an optional feature. Some people ignored it, some didn't understand it fully, and other people just made simple, forgetful mistakes. In each case, it left their Rails sites vulnerable. In Rails version three, filtering evolved into disallowing mass assignment by default and requiring all allowed attributes to be declared to the model.
```
Rails v3: whitelisting of attributes
```
In programming, when everything is disallowed by default, and we declare the items that should be allowed, it's called whitelisting and it's the opposite of blacklisting. **Secure by default is a better approach than insecure by default**.

It had a configuration though, that would allow us to turn whitelisting on or off, which some developers simply turned off rather than learn to use it properly. Again, leaving their Rails sites vulnerable. It was also a problem that these attributes were being declared as accessible or protected in the model. Often, an attribute is protected in some contexts, such as when the public submits a form, but should be accessible in other contexts, such as when an admin submits a form. And, by having it at the model level, it make it difficult to achieve.

```
Rails v4-5: strong parameters
```
Rails 4 and 5 solved both of these problems using strong parameters. Strong parameters can't be turned off with a simple configuration. They're strong, in part, because they're on by default and they make it hard for developers to get around them. In some sense, they protect you from yourself. The code for allowing or disallowing attributes has also been moved from the model to the controller so that each controller can manage its own list of whitelisted attributes. And, because strong parameters are turned on by default, we need to learn to use them.

## Strong Parameters
All we have to do is tell the params hash what attributes to permit on each request.
### Permit
```
params.permit(:first_name, :last_name)
```
**Permit is a method that marks the attributes as being available for mass assignment**. **By default, all values in the params hash are unavailable for mass assignment**. **We must whitelist the ones that we want to allow**.
### Require
```
params.require(:subject)
#returns :subject hash, similar to params[:subject]
```
There's another method called require and **require ensures that a parameter is present**. **If our attributes hash is assigned to subject, then we need to make sure that subject is in the params.**

It doesn't actually do any permitting, it just makes sure that the value is there and then it returns the value of that parameter. So, the two actually work together hand-in-hand.
```
params.require(:subject).permit(:name, :position, :visible)
```

**We have params, require subject, which requires that the subject is there, that that key exists, and returns its hash of values at the same time. And then out of those hash of values, we permit, name, position, and visible**. **That's our whitelist**. Those are the ones that we want to allow. Again, this permission is only as it relates to mass assignment.

Any other parameters will still be in the params hash and will still be usable as values. They just can't be mass assigned to an object without giving them permission. Now that we understand the problem with mass assignment and the solution that strong parameters provide, we're ready to fix our create action.
```
@subject = Subject.new(params[:subject])
```

So, instead of just you using params(:subject), we now know that we need to change this to
```
def create
  @subject = Subject.new(params.require(:subject).permit(:name, :position, :visible))

  if @subject.save
    redirect_to(subject_path)
  else
    render('new')
  end
end
```

```
@subject = Subject.new(params.require(:subject).permit(:name, :position, :visible))
```

params.require(:subject) and then permit, and then what we want to permit, name, position, and visible.

Now, we're starting to fill up a lot of space here and we're going to need to mark these as available again when we do our updating, as well. So, the **Rails convention is not to put this declaration here inside the create action, but instead I'll cut that, and let's just drop down to the bottom here, and we're going to create a private Ruby method**.
```
class SubjectsController < ApplicationController
  def index
    @subjects = Subject.sorted
  end

  def show
    @subject = Subject.find(params[:id])
  end

  def new
    @subject = Subject.new({:name => 'Default'})
  end

  def create
    @subject = Subject.new(subject_params)

    if @subject.save
      redirect_to(subject_path)
    else
      render('new')
    end
  end

  def edit
  end

  def update
  end

  def delete
  end

  def destroy
  end

  private

  def subject_params
    params.require(:subject).permit(:name, :position, :visible)
  end
end

```

And that Ruby method is going to be called subject_params, and the only thing that we're going to put inside there is that code that we just had.

So, now when I ask for subject params, guess what I'm going to get back. I'm going to get back the params, having required that the subject be there and having permitted the correct things. So, I can just use subject_params now up here inside new and I will get a permitted version that's available for mass assignment. Just like that. So, let's try it. Let's save our file, let's go into Firefox, and I'm just going to hit the back arrow so we get our form again, and let's submit the form one more time, Create Subject.

And this time it worked successfully. We now see our test subject did appear here, as the fourth subject, and it did execute our code successfully. If the subject saved, then redirect back to that subject's path. We did get redirected back to that path at the end. So now we've successfully processed our form data and added our new record to the database. We've coded both parts of the create portion of CRUD, displaying the form and processing the form. Next, let's look at how we edit existing record.
