- [Instructor] In this movie, we will learn to implement the show action, which is the other common read action used for CRUD.


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


In the last movie, we used index to return a list of records. One key difference about show is that it's going to require an ID, and that ID must exist in the database. Otherwise rails would return a record not found error. We can't view the details of a record if we can't look it up by ID or if it's not there. Let's add the link to the show page on the index template. We'll be sure to add that ID. In my list of subjects, I already have links for show but they're just place holders at the moment.
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
          <%= link_to("Show", '#', :class => "action show") %>
          <%= link_to("Edit", '#', :class => "action edit") %>
          <%= link_to("Delete", '#', :class => "action delete") %>
        </td>
      </tr>
     <% end %>
  </table>
</div>

```
Let's go into our code and right here where we have link to, show, let's add this URL.
### Three different ways that we can write this URL since we've defined our resourceful route on it.
**1.**
```
<%= link_to("Show", 'subjects/#{subject.id}', :class => "action show") %>
```
we could just say subjects and then slash and then inside here we could drop in subject.id, right, that would work. That would generate a URL that had subjects followed by the ID and that's what we're looking for.

 **2** Another way that we know we can do that is by using a hash.
```
<%= link_to("Show", {:controller => 'subjects', :action => 'show', :id => subject.id}, :class => "action show") %>
```
So we could have controller = subjects and let's have action = show and then let's have ID = subject.id. That would also work. Rails would take that hash, it would look at our resourceful routes, and it would determine what that URL oughta look like, and it oughta look like subjects/ and then the ID after it.

**3** Or the even better way and the way I'm gonna recommend that we do these is using those **resourceful route helpers**.
```
<%= link_to("Show", subject_path(subject.id), :class => "action show") %>
```
So we could just say subject_path and then inside subject.id or the ID is actually optional, we can just have subject.
```
<%= link_to("Show", subject_path(subject), :class => "action show") %>
```

So subject, path, subject and **that will generate the URL that we need**, so this is nice and easy to remember. Remember **it's singular because we're working with a singular subject and because we're working with that singular subject, we have to provide the ID as the first argument**. Let's save that, let's go back and let's just load up Firefox real quick. And now you can see we have the right URLs.

Firefox tells us down at the bottom this is gonna be subjects/1, this one'll be subjects/2, and this one'll be subjects/4. So all of those links are doing what we would expect. So **clicking on any one of those would create a new request which would be routed to our show action, and the request would include the subject ID**. So now let's switch back over to our subjects controller,
```
class SubjectsController < ApplicationController
  def index
    @subjects = Subject.sorted
  end

  def show
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
where we already have a placeholder for our show action. We know how to find a single subject in the database, we did that from the console earlier, so we just need to add that to our action here.
```
def show
  @subject.Subject.find(1)
end
```
So for example, we could do that with @subject = Subject.find(1) and that would find the subject with ID one and it would assign it to the instance variable subject, singular because we're only working with one and then our template would have access for that instance variable to be able to display it in a detail view. Now of course I don't wanna always find ID one. Instead **I wanna be able to vary the ID in the database query**. If you'll remember back to when we worked with the query parameters, **we can find the current ID from those parameters using params and then ID**.

```
def show
  @subject.Subject.find(params[:id])
end
```

So that's how I find out the current ID, I'm then going to ask the database to return the subject that has that ID and set it equal to this instance variable. Let's save that file, let's switch over to the show template

```
<h1>Subjects#show</h1>
<p>Find me in app/views/subjects/show.html.erb</p>

```


which is what will be rendered by default, and inside this show template I'm going to paste some more HTML. Remember you can pause the movie if you want to copy it down. Let's talk about what's in here. I've got a link at the top that says back to list. And where is it gonna take me? It's gonna take me back to that index page. This is the URL helper that will help me to do that.

The URL is just /subjects, but this is the URL helper that does the same thing. Then after that I've got a title here that just says show subject and then I've got another table that's going to just list off the attributes of this subject. I've got its name, its position, whether or not it's visible, once again I'm using that ternary operator to do that, and then it's gonna display created at and updated at. It's similar to what we had in the index template, we're just not going to have a list. It's not a list anymore, just gonna be looking at one single item, not an array of items.

So let's try it all out. I've still got my web server running over here. If not, you can start your web server up. And let's try clicking on one of these now. Let's try initial subject, let's click show. Notice the URL changed to subjects/1 and it worked. Here's where it routed, subjects/1 to the subjects controller show action. The params, see parameters, ID was equal to one. It went to the database and it got the record we wanted, where subject's ID was equal to one. And then it rendered the template that we wanted. Subjects/show.html.erb.

And here we are, here's what the template looks like, we've got details on each one and then we've got a back to list link we can click. And we go back to subjects again. Let's look at next subject, here's next subject, back to list, and then third subject, and again, back to list. Now that we've learned to implement the two read actions of CRUD, we're ready to move on to create, update, and delete. All of those actions are going to require forms and form processing, so we needed to start by learning the basics of working with forms in Ruby on Rails.
