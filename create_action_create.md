# How to write the create action.

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

In the previous movie, we added the new action. New and create work together as a pair and together they make up the create portion of CRUD. New displays the form, create processes the form.

### The four main steps in form processing.
1. We need to instantiate a new object using the form parameters.
2. We attempt to save that object.
3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.
4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.

You might be wondering how the save could fail. We haven't put any data validation into our application yet but eventually we will. Data validation ensures the data received from the form matches the criteria of the data we want to allow to go in the database. For example, if we want to limit the subject's name to be a maximum of 50 characters, then we add data validation, which would raise an error and cause the save to fail whenever the name is longer than 50 characters.

Even though we won't add data validation until later, we will include this step now because it is an essential part of the way forms are processed in the controller.

## Let's go into our subjects controller
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
and we already have our placeholder for our create action and we already wrote a form which will submit values to this action. So now what we need to do is handle the form processing. I'm going to start by pasting in those four steps that we just talked about so that we can look at each one of them.
```
def create
    # 1. We need to instantiate a new object using the form parameters.
    # 2. We attempt to save that object.
    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.
    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.
  end
```

So the first step, to instantiate a new object using the form parameters.
```
def create
    # 1. We need to instantiate a new object using the form parameters.

    @subject = Subject.new(params[:subject])

    # 2. We attempt to save that object.
    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.
    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.
  end
```
 We're going to have @subject equals Subject.new and then inside those parameters, we're going to put params[:subject]. So here we are, we have our params asking for subject that'll return all of the key value pairs which have been bundled up together as a form array and it will then populate our object with it. So it will be instantiated and it will have the correct values. So now, we just need to save that object.
```
@subject.save
```
But we want it to be conditional.
```
def create
    # 1. We need to instantiate a new object using the form parameters.

    @subject = Subject.new(params[:subject])

    # 2. We attempt to save that object.

    if @subject.save

    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.

    else

    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.

    end
  end
```

If the save succeeds, or if the **save fails and save returns true or false depending on whether it worked or not.** So that's very convenient because we can just put this then as **if subject.save and then if the save succeeds, else if the save fails**. Right? So we've got our conditional now. If subject.save, it succeeds then what do we want to do? **We want to redirect** to the list action. We know how to do redirect_to() and we could provide just a simple string for the URL.

We could also use our hash notation that we've seen before but we're going to **use those helper methods**. So we're going to use **subjects_path and that will give us the resourceful route back to that index page**. All right, so **if the save fails, we want to re-display the form, so that the user can fix problems.** Now, remember, **render create would be the default template that this would render**. Right? It would render just the create template but **there's not a create template**. **It's the new template**. That's what has the form. So what we want and actually have here, is tell it that it should render new.

Right? Now remember we can say template new in front of it but the shorthand is just to say new. Now this does not perform the new action. Remember that from when we talked about rendering and redirecting. **This just renders that form template** and remember, **our form template, wants to have an instance variable called subject available to it**. So it can display those values on the form. Well, we have that. **Create has already setup that instance variable here.** It has the submitted values in the form. So **those values that get submitted are in this object and this object will be used when the new template re-renders again**.

That's very handy because it would be very frustrating for a user if they filled out 40 different form fields and there was a problem with one field and they got back a blank form which they had to fill out again. Instead, **our form is going to be pre-populated with their submission**, so they can just fix the problems and then resubmit it. Last, I want you to notice that because we have this if else statement here, one of these two things has to be true. Either the save will succeed and we'll redirect or the save will fail and it will render a different template. In either case, we don't need a template for create.

It's either going to go to a new page, a new action or it's going to use the new template. So let's save this controller and let's try it out. Save it, let's go back to this page and let's hit reload just to make sure we get a fresh copy. I've already got my web server running, localhost:3000/subjects/new and now let's try it. Let's try creating one, we'll call this test subject and the position we'll make just four and visible will be false and then let's click submit. Okay, we got an error here.

Forbidden attributes error. It did not work. Now it didn't do our if or else because instead it gave us this error. So, what happened, is that when we submitted our form, we ran right into one of Rails' built-in security features and that's okay because it's actually a good feature that we want to make use of. We just have to learn how to make use of it. In the next movie, we'll learn how to fix it and we'll learn about mass assignment and strong parameters.
