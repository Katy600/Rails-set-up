- [Voiceover] Now that we understand the basics of forms, we're ready to implement the Create Actions in our controller.

|**CRUD**| **Action**| **Description** | **REST Route**|
|:-------|:---------:|:---------------:| -----------------:|
| Create | new       |Display new record form|GET/subjects/new
|        |create     |Process new record form| POST/subjects/create
| Read   | index     |List records           |GET/subjects
|        | show      |Display a single record|GET/subjects/show/:id
| Update |edit       |Display edit record form|GET/subjects/edit/:id
|        |update     |Process edit record form|PATCH/subjects/update/:id
| Delete |delete     |Display delete record form|GET/subjects/delete/:id
|        |destroy    |Process delete record form|DELETE/subjects/destroy/:id


The create part of CRUD is made up of two standard actions, New and Create, and they work together. The New action displays a web form that we can fill out and submit to the Create action. Create action handles the form processing.

###Let's focus on the New action in this movie.

Let's begin by adding a link from the Index template to our New action. You can see, I already have the link here for Add New Subject. Let's go and actually add the code that we need to make that work.

Inside my Index template,

```
<div class="subjects index">
  <h2>Subject</h2>
  <%= link_to('Add new subject', '#', :class => 'action new')%>

  <table class='listing' summary='Subect list'>
    <tr class='header'>
      <th>#</th>
      <th>Subject</th>
      <th>Visable</th>
      <th>Pages</th>
      <th>Actions</th>
    </tr>
    <% @subjects.each do |subject| %>
      <tr>
        <td><%= subject.position %></td>
        <td><%= subject.name %></td>
        <td class="centre"><%= subject.visible ? 'Yes' : 'No' %></td>
        <td class="centre"><%= subject.pages.size %></td>
        <td class="actions">
          <%= link_to("Show", subject_path(subject), :class => "action show") %>
          <%= link_to("Edit", '#', :class => "action edit") %>
          <%= link_to("Delete", '#', :class => "action delete") %>
        </td>
      </tr>
     <% end %>
  </table>
</div>

```

here's my Add New Subject,
```
<%= link_to('Add new subject', '#', :class => 'action new')%>
```
and right now, I just have a placeholder. Now we could just write out the URL for that.

```
<%= link_to('Add new subject', 'subjects/new', :class => 'action new')%>
```

It's subjects slash new. That's the restful route that we created for this. But it's better if we can use the route helper, the resourceful route helper that we talked about at the end of the last chapter.

```
<%= link_to('Add new subject', new_subject_path, :class => 'action new')%>

```
So what that would be here is new underscore subject underscore path. And that reads well too. We're gonna link Add New Subject to New Subject Path.

It's the path that we want to go to, the path for a new subject. So let's save that and now we'll have the correct link. Let's go back over and let's reload the page. You should still have your web server running. If not, you'll want to start it. Add New Subject, and you can see, it's gonna take me to Subjects slash new, that's where we want it to go.

## So next, let's add the Form template that we're going to need.

 So let's go into the New Templates.
 ```
 <h1>Subjects#new</h1>
 <p>Find me in app/views/subjects/new.html.erb</p>
 ```
Subjects New dot html dot erb. And I'm going to just paste in some html again. Remember you can pause the movie if you want to copy this down.
```
<%= link_to("<< Back to Link", subjects_path, :class => 'back-link') %>

<div class="subjects new">
  <h2>Create subject</h2>
  <%= form_for(@subject, :url => subjects_path, :method => 'post') do
    |f| %>

  <table summary="Subject form fields">
    <tr>
      <th>Name</th>
      <td><%= f.text_field(:name) %></td>
    </tr>
    <tr>
      <th>Position</th>
      <td><%= f.text_field(:position) %></td>
    </tr>
    <tr>
      <th>Visible?</th>
      <td><%= f.text_field(:visible) %></td>
    </tr>
    <tr>
  </table>
  <div class='form-buttons'>
    <%= f.submit("Create Subject") %>
</div>

```
Then let's talk about what's here. At the very beginning, I've just got a link for Back to list, which goes to Subjects Path, that's the Index path that we just saw. And then I've got a form. And I've got my form using Form Four, just like we saw in the last movie, it's using Subject. And then, I've added a couple of extra options to it. I've told it to specify the URL as Subjects Path and the method as being post.

If you recall, in our resourceful routing, whenever we post a request to Subjects underscore Path, that's going to create a new object on our resource, that's a Create.

```
<%= form_for(@subject, :url => subjects_path, :method => 'post') do
```
Now, I wanted to show you these so that you knew that they were there and you can customise them if you want. But we don't have to specify them. **The Form Four helper is smart enough to know that Subject is a new object and it knows that the way that you create a new object when you're using resourceful routes, is that you post a request to that URL**. So we don't have to actually specify it, that's nice.
```
<%= form_for(@subject) do
```
Then after that, you can see that I've got a code block and I've got this local block variable F that's available to me. It's going to represent the subject throughout the rest of the form.

So I have a text field for name, text field position and text field visible with a submit tag at the bottom.
## Now let's save it and let's switch over to our Subject's controller.
```
class SubjectsController < ApplicationController
  def index
    @subjects = Subject.sorted
  end

  def show
    @subject = Subject.find(params[:id])
  end

  def new
  end

  def create
  end

  def edit
  end

  def update
  end

  def delete
  end

  def destroy
  end
end

```
And we have a placeholder for our new action. So what code needs to go in this action? What instance variables do we need to set for our form template to have? There are actually two answers. The first is, if we were to make some changes to the way that we'd written our form, it would be possible not to instantiate anything here at all. However, I think that it's of best practice to go ahead and define the subject here as subject dot new.
```
def new
  @subject = Subject.new
end

```
That's also the way that we've written our form.
```
<%= form_for(@subject) do
```
It's gonna be required because of the way that our form four works. It's going to expect to have an instance variable. **Having an object here, though, allows us, or rails, or even our database to set default values for the object's attributes**. **If left out, all those form values would just simply be blank** but by defining it, then we'll get any default values that have been set. Those will be passed along to our form. So let's save this file and then let's go back and let's try our page.

Let's reload, make sure we've got a fresh copy. Add New Subject, here's my form. Create Subject. Now notice, it went ahead and pre-populated Visible with False. That was a default value that we gave to the databse. When we created the My SQL Table, we told it that it should default to False. So it's actually pulling that value out of the database and presenting it to us in the form, so we have the opportunity to edit it. We also had the opportunity to set other default values ourself.
### Set default values
```
def new
  @subject = Subject.new({:name => 'Default'})
end
```
For example, inside New, let's just add a hash and we'll put in Name, it's gonna be default.

Let's save it, let's go back and reload our page. Look at that, we get that name in there. So we have the ability to put default values on the form or to pull them out of the database. Now we have our form for our new action. It instantiates any objects that we need, uses the rails helper to create the html form, using that object while it does it, and then returns the form back to the browser. Now we're ready to process this form and create the subject using our Create action.
