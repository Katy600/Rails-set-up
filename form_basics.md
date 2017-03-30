# Form Basics

There are a couple of points that I want you to remember about working with Rails helpers to generate HTML for our forms.

### Rails/erb vs. HTML

 1. The first is that **anything that we can write with the helpers can also be written using simple HTML**. That's all the helpers are doing, they're helping us write HTML and the end result that gets sent back to the browser is always just going to be plain HTML.

2. Writing code using these helpers is almost always easier, more powerful, and includes lots of extra benefits than if we use simple HTML alone.

## Standard HTML form.
Again this not Ruby on Rails, this is just basic HTML.
```
<form action="/subject" method="post">
  <input type="text" name="name"/>
  <input type="text" name="position"/>
  <input type="text" name="visible"/>

  <input type="submit" value="Create Subject"/>

</form>
```
Notice that my form has three text input fields for name, position, and visible, followed by a submit button labelled Create Subject. Notice that this form is going to use the method POST, that's different from clicking on a link or URL.

Links and URLs are GET requests, forms are POST requests. Remember, the method is important when we're using REST to route our requests. The form's action attribute has a value of slash subjects. That's the URL where the form will submit its data. Our resource for routing will direct a POST request to that URL to the subjects controller and to the create action. POST parameters in the controller are going to be accessed the same way as we access GET parameters.
```
params[:name]
params[:position]
params[:visible]
```

We just say params and then the key we want, name, position, or visible. Just like we did for params ID. That may be new to you, if you're familiar with working with other languages like PHP which separate out GET and POST parameters. In Ruby on Rails they're combined together. If we want to use those parameters to construct a new subject, we could put each one into a hash and then pass them into Subject dot new.

```
subject = Subject.new(
  { :name => param[:name],
    :position => param[:position],
    :visible => param[:visible] }
    )
```

This is the simplest way to work with forms in Rails. But there are many improvements that we can make. The first improvement is that it's tedious to build up this hash by grabbing each parameter by name.

Imagine if we had a form that had 40 fields in it, it's much better if we can do it using form arrays.

## HTML Form: Arrays of Parameters

Now, keep in mind that form arrays are just a basic part of HTML, it's not special to Ruby on Rails.
```
<form action="/subject" method="post">
  <input type="text" name="subject[name]"/>
  <input type="text" name="subject[position]"/>
  <input type="text" name="subject[visible]"/>

  <input type="submit" value="Create Subject"/>

</form>
```
In this version of the HTML form, notice that the name value for each of my inputs has changed. Where before we had name equals name, now we have name equals subject and then name in square brackets after it. ***This syntax submits these form values grouped together in an array named subject***.

**It's a form array**, and **Ruby on Rails makes use of that form array and groups the parameters together under a common key, in this case subject**.
```
params[:subject][:name]
params[:subject][:position]
params[:subject][:visible]
```
This is an improvement because **now we have access to those parameters as a hash just by calling param subject**.
```
param[:subject]
#returns..
{:name => 'About us', :position => '5', :visible => '1'}
```
When we want to create a new subject, all we have to do is pass in param subject to Subject dot new and it will include all of those key-value pairs that just came in on the form.

```
subject = Subject.new(params[:subject])
```

We no longer have to build the hash ourselves using every attribute, the form does it for us.

Now so far this has just been basic HTML, no Ruby on Rails code. Let's go back and look at that nested parameters version of our form again.
```
<form action="/subject" method="post">
  <input type="text" name="subject[name]"/>
  <input type="text" name="subject[position]"/>
  <input type="text" name="subject[visible]"/>

  <input type="submit" value="Create Subject"/>

</form>
```
I'm gonna show you a version that uses the Rails helpers next.
```
<%= form_for(@subject) do |f| %>

    <%= f.text_field(:name) %>
    <%= f.text_field(:position) %>
    <%= f.text_field(:visible) %>

    <%= f.submit("Create Subject") %>
<% end %>    
```
In this version, I'm gonna use the rails form_for helper which **uses form_for and then the argument that it accepts is an object**. So I'm gonna **pass in an instance of my subject.** I have a **subject object** that I'm passing in, and **it's going to take that and create the form that I need**.

It's also going to do **a code block, that's everything from do down to end,** and it's gonna have a **local code block variable which is f and f is gonna be available to me throughout the rest of that block**. **F** is a **form builder object**, and **it knows about the object that I just created, subject**. So it's sensitive to that fact. Now, **each of those texts fields is generated by calling the text_field method on the form builder object, and telling it which attribute we want**, name, position and visible. The end result again is exactly the same HTML as what we just saw in the last movie.

### What are the benefits to using rails helpers?
1. This form_for object knows whether the object is a saved object or not. If it's a saved object, then it knows that we're going to be doing an update action on it. If it's not a saved object, it knows that we're doing a new action, and it adjusts the form accordingly for us.

2. If it's an existing object, it knows to pre-populate all of those text fields with the existing values.

3. If it's a new object, then it knows not to do that, and it won't bother putting those values in there.

So now we can have code that defines one form, but that form can generate a smart, dynamic HTML form for us and that's a lot better. Now that we have a fundamental grasp of forms, let's see how we can use them as we implement the create part of CRUD in our controllers.
