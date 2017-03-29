- [Instructor] In this chapter, we will discuss several concepts which are important for the design of Rails applications. These concepts will affect the way that we work with URLs, how we write controller code, and how our controllers manage our models and database data. We're gonna start by talking about the concept of CRUD. Let's begin with reviewing the MVC web architecture diagram.
![alt text](https://defendingdiaspora.files.wordpress.com/2013/07/mvc_archi1.png)˜
 First, we learned how to use controllers and views to create dynamic pages and return them back to the browser. Next, we shifted our focus and we learned to interact with the database by creating models and associations. And we worked with those, not from a browser, but via the Rails console. Now, we need to put the two together and learn ***how to work with the models inside our controllers***. Then we'll be able to **manipulate our database data from the browser**, in the same way that we did from the console. **The way that we're gonna work with the models and our controllers is using CRUD**. ***CRUD is an acronym for create, read, update and delete***. And those are four actions that are the main ways in which we interact with our models and with the database. Because these four are so common, in Rails we have standard controller actions for implementing them.

You don't have to use these standard action names but they are the Rails convention and life will be easier if we stick with them. For each element of the CRUD, there are two actions associated with it.

|**CRUD**| **Action**| **Description** | **Example of URL**|
|:-------|:---------:|:---------------:| -----------------:|
| Create | new       |Display new record form|/subjects/new
|        |create     |Process new record form| /subjects/create
| Read   | index     |List records           |/subjects
|        | show      |Display a single record|/subjects/show/:id
| Update |edit       |Display edit record form|/subjects/edit/:id
|        |update     |Process edit record form|/subjects/update/:id
| Delete |delete     |Display delete record form|/subjects/delete/:id
|        |destroy    |Process delete record form|/subjects/destroy/:id

Let's begin by looking at the top two lines, create. **Create has two actions, new and create**. **New** will **display a web form which will be filled out with the data that we want to send to the database**. Then *when we submit that web form, it's gonna submit to the create action.* Create will *process the form and either save the information to the data base or handle any errors that come up*.

So **new displays the form**, **create processes the form**. It's a two step process. Let's skip over read for a moment and go down to **update** because it works very similar to create. The difference is now with update, **there's existing data in the database**. So it finds the existing data, **we get a web form which is pre-populated with that data, we make any changes we want**, we submit it to the **update action, and the update action will process the form and submit those changes to the database**. So edit displays the form, update processes the form.

And delete works the same way. **Delete displays the information about the record we're about to delete**. **Destroy performs the destruction**. The fourth part of CRUD, **read, works a little bit differently**. It does not have a display the form, process the form pairing. Instead it has **two actions that are different**. The first action is **index**, which **returns a list of records**. **Show is an action which will display a single record**, that is a detailed view of a single record in the database. So the first one is a list view, the second is a detail view.

Notice in the far right column of this table, there's a list of URLs which will be used to request these actions from the subject's controller. Most of these are self-explanatory. ***Index could include the word index after subjects but it's usually omitted and used as the default value***. Notice that **show, edit, update, delete and destroy all perform their work on an existing record**. So they **include the record ID in their URL**. Index, new and create do not require an existing record so they do not. Together these eight actions are the core of working with CRUD in rails.

You will use them over and over again in every application that you build. Over the next two chapters, we're gonna learn how to implement them and use them to manage our subjects, pages, and sections in our CMS. I'll demonstrate how to add them for subjects and then later you'll have the opportunity to try adding them for pages and sections on your own. We're going to be dividing the CRUD for subjects, pages, and sections into three separate controllers. The **subjects controller, the pages controller, and the sections controller**. **You typically have one controller per model**.

It helps you to keep your code organized. And **you're going to use plural names for your controllers**. Notice it's **subjects controller not subject controller because we are controlling all of our subjects with this controller not just one**. This also allows for **common sense URLs**. For example, we will have *subjects/new, pages/new, and sections/new, subjects/edit, pages/edit, and sections/edit and so on.* These ***URLs clearly define the model which is being manipulated and the action which is being performed on it.***

Let's generate a subjects controller and add placeholders for the basic CRUD actions. So inside my simple CMS, in my controllers, I still have my demo controller that we created earlier. We're gonna leave that there but we want to create a new controller now, for our subjects. You may recall the way we did that was from the root of our application, we typed rails, generate, and then controller and then what we wanted to name our controllers.
```
rails generate controller Subjects
```
Subjects, plural. Now before you hit return on this and generate the controller, I want to show you that there's also an option for generating controllers, where we can specify the views that we want it to create at the same time.

```
rails generate controller Subjects index show new edit delete
```

It's a helpful shortcut. So we'll put a space and then let's type out the actions that we have which will need views associated with them. We have our index and show, that's our list and our detail views. We're also gonna need forms for new, edit, and delete. Now that's only five of our eight actions, the other three are create, update and destroy but those don't actually have views associated with them. Those are just actions which do the form processing. So I don't need to have a view generated for them, just these five.
```
katymccann (master *) simple_cms $ rails generate controller Subjects index show new edit delete
Running via Spring preloader in process 14102
Expected string default value for '--jbuilder'; got true (boolean)
Expected string default value for '--helper'; got true (boolean)
Expected string default value for '--assets'; got true (boolean)
      create  app/controllers/subjects_controller.rb
       route  get 'subjects/delete'
       route  get 'subjects/edit'
       route  get 'subjects/new'
       route  get 'subjects/show'
       route  get 'subjects/index'
      invoke  erb
      create    app/views/subjects
      create    app/views/subjects/index.html.erb
      create    app/views/subjects/show.html.erb
      create    app/views/subjects/new.html.erb
      create    app/views/subjects/edit.html.erb
      create    app/views/subjects/delete.html.erb
      invoke  test_unit
      create    test/controllers/subjects_controller_test.rb
      invoke  helper
      create    app/helpers/subjects_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/subjects.coffee
      invoke    scss
      create      app/assets/stylesheets/subjects.scss
katymccann (master *) simple_cms $
```

Let's hit return, it'll take a moment while it creates our subjects controller. Now if we come over and look, we have a subjects controller here.
```
class SubjectsController < ApplicationController
  def index
  end

  def show
  end

  def new
  end

  def edit
  end

  def delete
  end
end

```

It went ahead and gave us some stubs for our actions. We have index, show, new, edit, and delete. And if we look in our views, you'll see that we have a subjects directory now and it includes placeholders for all of these different views. They just have some really simple text inside of them but we have a view there ready for us to fill out. So that's a handy trick. It really can save you a lot of time. Let's go ahead and add the other actions that we're gonna need, which don't have views.

In addition to new, we're gonna have create. In addition to edit, we're going to have update. In addition, to delete, we're going to have destroy. So that gives us the eight actions that we need.
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
These are the ones for reading, index and show. Here's the ones for creating, new and create. And here's the ones for editing, edit update. And last of all, we have delete and destroy for deleting. We will learn to populate these actions in the next chapter. In order to use these actions, remember that ***we also need to add them to our routes file***.

And that can be in our config directory. And inside routes. Now it gave us some placeholders for all of these actions here,
```
Rails.application.routes.draw do

  get 'subjects/index'
  get 'subjects/show'
  get 'subjects/new'
  get 'subjects/edit'
  get 'subjects/delete'

  root 'demo#index'
  get 'demo/hello'
  get 'demo/index'
  get 'demo/other_hello'
  get 'demo/lynda'
end

```
subjects, new, edit, and delete and we would also need to add routes for the other actions but instead of taking the time to write out those routes, let's instead learn about rest and resourceful routes first. Because I think that's a better option.
