One benefit to using resourceful routes is how much typing it saves us. And the reason why that's possible, is because of Rails sensible defaults and because there's some good helper methods that we can use in our links and in our forms. Let's take a look. In the past,

when we've wanted to generate a link, we wanted to provide a URL. We could either provide a string, that was actually the URL, or we could have Rails construct that for us by giving it a hash. And we would just specify the controller and the action that we wanted and Rails would handle going through the routes file to figure out how to turn that into a URL.

Now, with resourceful routes, we have some helpers that can help even further. Instead of having to write out
```
{:controller => 'subjects', :action => 'show', :id => 5}
```

 we can use the helper and simply say,
```
subject_path(5)
```
And **that will generate the exact same URL**. But it will reduce the amount of code that we have to write and maintain. So, that's an example of a resourceful URL helper. Let's talk about what all of them are.

# Resourceful URL helper

| **HTTP Verb** | **URL** | **Action** | **URL Helper** |
|:-------------:|:-------:| :---------:|:--------------:|
|GET|/subjects|index|subjects_path|
|GET|/subjects/:id|show|subjects_path(:id)|
|GET|/subjects/new|new|subjects_new_path|
|POST|/subjects|create|subjects_path|
|GET|/subjects/:id/edit|edit|edit_subject_path(:id)|
|PATCH|/subjects/:id|update|subjects_path(:id)|
|GET|/subjects/:id/delete|delete|delete_subject_path(:id)|
|DELETE|/subjects/:id|destroy|subject_path(:id)

In this table, I have the same first three columns that we saw in the last movie. I have the HTTP verb, the resourceful URL, which when combined together, give us the correct controller action.

**The fourth column lists the URL Helper method that Rails provides to generate the correct URL and HTTP verb combination**. It's not so different from the URLs themselves, just rearranged a bit to be more readable. But notice one thing important, **almost all of them use subject, singular**. And that's because they're gonna be dealing with a **singular member of the resource**. In the last movie we talked about the difference between member routes and collection routes. **With index and create, we're gonna be working with a collection as a whole**, either listing the collection or adding a new item to it.

So, they use the plural form. If the fourth column seems like a lot of information that you have a memorise, notice that there's a pattern. And there's really just **two main parts**. There's **subjects_path, plural**. And there's **subject_path, followed by id as an argument. And it's the singular form**. The other three cases just put **new, edit and delete in front**, the same way that the resulting URL adds new, edit and delete at the end. And remember, those are the three convenience actions, which **simply display a web form to help us to compose data before submitting it**.

**These URL helpers will allow us to generate the correct URLs**. For example, if we're using our link to helper,
```
<%= link_to('All Subjects', subjects_path)%>
```
instead of providing a hash as an argument, we can use that URL helper to give us the same effect. So, link to, all subjects and then subjects path is **going to create the same string, the same URL in the link that will point to subjects slash index**.
```
subject/index
```

If we wanted to add a query parameter as an argument,
```
<%= link_to('All Subjects', subjects_path(:page => 3))%>
```
then you would just put that in parentheses so it'd be passed in as an argument to the helper method.

If we wanna look at a particular item,
```
<%= link_to('Show Subjects', subject_path(@subject.id))%>
```

we would use subject_path, that's the singular version, and then we need to pass in the id as the first argument. Here I'm using @subject.id. Now, most of the time in Rails we can actually leave off the .id and just by passing in the subject, Rails will by default give us its id. It will know, hey, I have an object here, but I'm looking for an id, so let me use the id of this object. So, having .id is optional. If you wanted to pass in some query parameters with that

```
<%= link_to('Show Subjects',
      subject_path(@subject.id, :format => 'verbose'))%>
```

, you would just put a comma after that first argument and provide any additional parameters next.

And then last of all, if we wanted to edit a subject

```
<%= link_to('Edit Subjects', edit_subjects_path(@subject.id))%>
```


we could use edit_subject_path and then provide either just subject or subject.id Now, that shows how to use these URL helpers with links. And that's all we've learned how to do so far. Our forms will also need to use post, patch and delete. But, we'll come back to those when we know more about how forms work in Ruby on Rails. Now that we know about CRUD, REST and how to use resourceful routes and the route helpers, we're ready to learn to write controller code, which can perform CRUD operations on out models.
