- [Instructor] We've added code which will create records from our controller.

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


 Now, we want to be able to update existing records from the controller. Like create, there are two standard Rails actions for updating. **Edit displays the form, while update processes the form**. In fact, this two-step process of updating records is almost exactly like the process we just saw for creating them.

 ### Two key differences.
 1. is that we need to make sure that we have an ID. **New and create didn't need an ID, because there was no record in the database yet**, but edit and update require there be an ID so we can find that existing record. Once edit retrieves that existing record, it will use its values to pre-populate the form. Update will also **need to find the object before it can update it**.

 2. Form processing. **Create instantiated a new object, and then saved it. Update is going to find an existing object, and then use update attributes in order to update its data,**


 Let's add them to our application. Let's begin by going to our index page, and let's make this link for edit work, so that it will go to the correct template.
 ```
 <div class="subjects index">
  <h2>Subject</h2>
  <%= link_to('Add new subject', new_subject_path, :class => 'action new')%>

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

Let's go into index, down here we have our link to edit,

```
<%= link_to("Edit", '#', :class => "action edit") %>
```

and instead of this placeholder, we want to put in the action for edit. Now, we know we could put
```
<%= link_to("Edit", 'subject/1/edit', :class => "action edit") %>
```

in subjects one edit, that's the URL that we want to actually edit subject one, or we could use hash notation, but we're going to try and make use of those resourceful helpers as much as possible.
```
<%= link_to("Edit", 'edit_subject_path(subject)', :class => "action edit") %>
```
We have edit subject path, and then the subject that we want, and **that will pass in its ID by default**. This will be the same as what we had for show, this part, you see it's the exact same as up here, but we've added edit underscore in front of it, and that's what will take us to the correct template.

If we go back over, and we reload that page, initial subject, if we scroll over edit, you will see that I get the correct thing, subject slash one slash edit. Firefox tells me down the bottom, subject slash two slash edit. Now let's trump to our controller,
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
    # 1. We need to instantiate a new object using the form parameters.
    @subject = Subject.new(subject_params)
    # 2. We attempt to save that object.
    if @subject.save
    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.
    redirect_to(subjects_path)
    else
    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.
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

and let's write the edit action that we need there. In the edit action, we need to find the subject that we're going to edit, so that we can pre-populate the form on the edit template with its current values. How do we find the subject that we want? Well, we do it exactly like we did here, under show.
```
def edit
    @subject = Subject.find(params[:id])
  end
```
I'm just going to copy this same line, bring it down here, because the process is the same. When we want to edit a subject, we find it, and we assign it equal to at subject, that makes it available to our template. Let's save the controller, and now, let's switch over to our form template, under edit dot html dot ERB.
```
<h1>Subjects#edit</h1>
<p>Find me in app/views/subjects/edit.html.erb</p>

```

Now, we need an edit form. We already have a form for new that we created earlier. As a starting place, let's copy that form. I'm going to select all, copy it, and then switch back to our edit template, and let's paste it in there.

Now, there are going to be a couple of small changes that we need to make to this form, but they are very small. I'm just going to change the CSS class here from subjects new, to subjects edit, and instead of create subject, it's going to say update subject. It's still going to have a link back to the subjects list, and the form is going to be identical. Now, that's not always 100 percent the case, but here, form for is smart enough to know that subject is an existing record, and therefore, it will know what kind of a URL it needs to generate.
```
<%= form_for(@subject, :url => subjects_path(@subject) :method => 'patch') do
```
I could also specify it like this, the ***URL should be subject path, with the subject***, the path that I need, and **when put together with the patch method**, that will **update this subject**, but **form for will do that for me, so I don't need to specify it**.
```
<%= form_for(@subject) do
```
Then, all the rest of the form is going to be identical.
```
<%= link_to("<< Back to Link", subjects_path, :class => 'back-link') %>

<div class="subjects edit">
  <h2>Udate subject</h2>
  <%= form_for(@subject) do
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
    <%= f.submit("Update Subject") %>
</div>
<% end %>

```

Usually, the process of creating and updating records is pretty similar. We usually want to have the same data available for updating in both cases. Then, last of all is **create subject, let's change this to update subject at the bottom**.

All right, let's save our page, and let's go try that out. Let's come back over here. Under test subject, let's click edit, and there we go. We get our form, you see the URL up here is subject slash five slash edit, I get my form, and it has the values from the database pre-populated. Now, before we move on to the update side of things, let's just take a look at the web developer tools at the page source. Now, inside the page source, ignore everything the top of the top there. Jump down here. After you see update subject, look for the form.
```
<form class="edit_subject" id="edit_subject_2" action="/subjects/2" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="&#x2713;" /><input type="hidden" name="_method" value="patch" /><input type="hidden" name="authenticity_token" value="OnEyUS5Buf0wTHE92DGPinrGapsFNgMtY1JoDi5nRcwHVimytqMnDR6koYPjTzYlMqoB5Q6mBfEUc1SmDlGEkQ==" />

```
Form, right here. Notice that the form action is that it's going to submit to subjects slash five, that's what we would want, and **it's going to be a post request**. Now remember, **we actually want this to be a patch request if we're using rest, but HTML support for patch is spotty** at best, so it's actually **going to use a post request, which is very well supported**. Then, if we scroll over, you will see that there is a **hidden input rate here that says that the method is going to be patch, and that's what Rails is going to use**.

Rails is going to say, oh, even though this doesn't come in as a patch request, because I see the patch value being submitted in it, I'm going to treat it like it was a patch request. Okay, so we can close of the page source for that.
#### let's go into our controller, and let's write that update action.
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
    # 1. We need to instantiate a new object using the form parameters.
    @subject = Subject.new(subject_params)
    # 2. We attempt to save that object.
    if @subject.save
    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the index action.
    redirect_to(subjects_path)
    else
    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.
    render('new')
    end
  end

  def edit
    @subject = Subject.find(params[:id])
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
When we submit the form, it's going to submit to update, what do we want our code to be for update? It's going to be very similar to what we had for create. Let's start out by just copying all of this code, and let's put it down here, inside update.
```
def update
    # 1. We need to find a new object using the form parameters.
    @subject = Subject.find(params[:id])
    # 2. We attempt to update that object.
    if @subject.update_attributes(subject_params)
    # 3. If the save succeeds, we will redirect the user somewhere. In our case, we would redirect to the show action.
    redirect_to(subject_path(@subject))
    else
    # 4. If the save fails, then we want to re-display the form so that the user can fix the problems and resubmit the form.
    render('edit')
    end
  end
```
Instead of starting with a new object though, **we're going to start with an existing object**. How are we going to get that existing object? The same way we did with edit. We're going to find a new object, right here, and then instead of saving the object, we're going to **update the object**. I'm going to just leave this line here for a second. We're not going to use it, but I just want to leave it, Because **we're going to update the object using update attributes**, and then, **we want to pass in a hash of the new attributes that we want on the subject**.

We need to send in a hash, and **we could use params subject**, and that would give us back our hash,
```
@subject.update_attributes(params[:subject])
```

 but remember, from our discussion about mass assignment and strong parameters, **update attributes is mass assigning values**, so **we need to use the permitted values**, the ***whitelisted values, which subject params gives us***. I'll just paste that in there,
 ```
 @subject.update_attributes(subject_params)
 ```

  and now we can remove this line. It's going to update it with values that we've allowed to be mass assigned. Just as a refresher, remember that we have subject params down here, that handles params, it makes sure that there's a subject, and it permits the name, the position, and visible.

Now, if the save succeeds, we probably don't want to redirect back to the index action. In this case, let's redirect back to the show action instead. What is the path for the show action? Its subject path, and then at subject. Once we successfully updated in object, we will go back to its detailed view, and take a look at that, but if the save fails, then we want to redisplay the form. The form, remember, is our edit template, so we're going to change this to edit, let's save it, and let's try it out. Let's come back over here to this page, and let's change it from test subject position four, let's make it position five.

Update subject, and you can see that it did successfully update our subject. I'm now on the subject detail view page, the position has changed from four to five, and you can also see that the updated time has been updated as well. We'll click back to list, now here we are test subject again, let's try editing it one more time. Let's change it back to position four, update subject, and there it is again, updated back to four, and it changed our updated time at the same time. Play around with it a bit so that you get familiar with this flow, and then when you're ready, let's move on to see how we can go about deleting records.
