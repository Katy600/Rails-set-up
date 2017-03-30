# How to implement the basics of CRUD in our controllers and in our views.
In the last chapter, we already created a subjects controller and we set up the routes, the actions and the views that we'll need for all eight parts of basic CRUD. Now, we're going to start by **learning to implement the index action**.

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


**Reading records from the database is usually handled by two standard Rails actions: Index and show**. **Index displays a list of records**, while **show displays a detailed view for a single record**.

The two actions work together as a common application design pattern. **The application displays a list of items**, the user clicks a link for one of those items, then **the application shows the user a detail page for that item**. The user reviews the details, and then clicks a link to go back to the main list. Even if you've never put these exact terms to it before, I'm sure you're familiar with the concept from other websites. Let's see how we can add the index action in our subject's controller. Let's begin by going into our controllers, into the subjects controller that we created earlier. You can see that we already have a placeholder for our index action.
```
class SubjectsController < ApplicationController
  def index
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
Now, at the moment, it's not doing any code inside of that action, and that's what we need to add now. So, what code would be appropriate? What do we want it to do? **We want it to find all the subjects so that we can list them**. So, **the setup that it needs to do for the view is to find those subjects and assign them to an instance variable, so that the view template can access that list**.
```
def index
    @subjects = Subject.all
  end
```
So, let's create a list called @subjects. **It's going to have an @ symbol in front, so it's an instance variable**. **Let's tell our subject class that we want to get all subjects**. That would work, **that would return all subjects and assign it as an array to the instance variable subjects**.

Even better though, would be if we sorted this list.
```
def index
    @subjects = Subject.order(:position )
  end
```
Let's do order, and then we could have position. Or even better, if you remember in our model, for subject,
```
class Subject < ApplicationRecord

  has_many :pages

  scope :visible, lambda { where(:visible => true) }
  scope :invisible, lambda { where(:visible => false) }
  scope :sorted, lambda { order("position ASC")}
  scope :newest_first, lambda { order("created_at DESC")}
  scope :search, lambda {|query| where(["name LIKE ?", "%#{query}%"])}
end

```
we talked about named scopes. And **we created a named scope already here that's called 'sorted'**. And that's what it does, **it sorts by position**. So, let's use that instead.
```
def index
    @subjects = Subject.sorted
  end
```

So, let's put 'sorted'. Now, we could also add a line here that says render('index')
```
def index
    @subjects = Subject.sorted
    render('index')
  end
```
and **that would tell it which view template it should use**. ***But remember, Rails has sensible defaults, so we don't have to do that***.

And the Rails convention is usually to leave it out. Not to bother writing additional code. By default, it's going to look for that index template and that's what it's going to render. So, that's all we have to do. We've now set the stage for our index action to do its thing. Let's save this page. And now, let's jump down to our views.
```
<h1>Subjects#index</h1>
<p>Find me in app/views/subjects/index.html.erb</p>

```

 And inside our subjects directory, here is index.html.erb. **That's the template that the subjects controller index action will render by default.** So now, inside this template, **we need to add the code which will list out the subjects**.

Rather than have you watch me type all of that out, I'm going to paste in some HTML, and then we can talk about it.
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
    <% @subject.each do |subject| %>
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
I've just got a <div> here, surrounding the whole thing subjects index, just sort of labelling it by its controller and its action. I like to do that, it makes it easy to address each of the templates from the CSS. And then I've got an <h2> at the top that just says 'Subjects'. I'm going to put a link here for adding a new subject. We'll come back and actually add the action for that later. Then, I've got a <table>.

I'm going to list these subjects out in a table, this is an HTML table. It has a header row at the top, that's what this is here. Each of these are table header cells. And I've got a label for each of those columns at the top. Then here's the real meat of it: I'm taking that instance variable '@subjects', and the way that Ruby's 'each' works, is that it goes through each element of the array, and one at a time, it takes that element and it assigns it to the local block value 'subject'. So, that's going to be the variable that I'm going to be working with inside this code block.

The code block is everything from 'do' down to 'end'. So, inside there, I'm going to output another row. That's the <tr> tags. And then each one of those is going to have a table data cell. The first one will be subject.position. Then, I'll have the name. Then, I'm going to display whether or not it's visible. What I'm using here is called a 'ternary operator'. If you've never seen this before, it's a shorthand for writing an 'if, else' statement. So, it's going to check and see whether visible is true or false. The question mark then says "Alright, after that comes what we do if it's true." So, if it's true, we're going to output 'Yes'.

If it's false, we're going to output 'No'. It's a one-line way to write "If visible, then display this. "If not visible, then display that." And then we have subjects.pages.size, that will tell us how many pages there are. And then we have a cell that's going to contain three links: They're going to be actions. One for 'show', one for 'edit' and one for 'delete'. And we'll come back and write the URLs for those a little later, as well. Okay, so now that we have the HMTL for this, let's try it out. Let's save this file.

Let's come over to our Command line. Let's launch Rails Server. Not Rails Console, but Rails Server. Because we're going to bring it up in our browser now. And inside the browser, we're going to be going to localhost:3000/, and if you remember, the URL that we use in restful routing, to get to the index action is just 'subjects'. So, that's what we have our route set up for. Just subjects by itself. Subjects/index would be a default. But subjects by itself is going to route it to the right place. So, there we are.

We have 'Subjects' at the top. We have all of our links. The placeholders for the different links we've created. And you can see that we have a list. Here's the header at the top. And here's each one of those: One, two, and three. Initial subject, next subject and third subject. Now, for the first time we've completed the full cycle of working through Rails' MVC architecture.



### Let's review it to make sure you understand all the parts.
**We entered a URL here**, which was 'subjects'. And then **Rails took that URL 'subjects' and determined, using our resourceful routes that that ought to go to the Subjects Controller and to the 'index' action**.

Then, in our **subject controller 'index' action, it went to the database**. Using our **subject model, it got a list of all the subjects, and it sorted them**. It assigned them to an **instance variable: '@subjects'**, which our **template then had access to**. Our **template goes through that array of subjects, and outputs each and every one to generate a dynamic view**. And then at the end, **that view is sent back to the browser**. Now that we have a working index action, which can display a list of all our subjects.

Next, let's talk about how we implement the 'show' action.
