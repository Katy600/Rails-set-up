REST still lets us use the basic CRUD actions that we've already put in our controller, but the URLs which we use to access them will be different.
### These are called Resourceful Routes.
Because they're closely aligned with REST, you'll also sometimes hear them called **RESTful routes**. We could continue to simply write match routes and use the principles of REST, and at the moment, in Rails 5, you can even use the default route structure, though that may go away later. So

### Why use resourceful routes then?
1. It is the Rails default
2. They are optimised for REST.
They really make it easy to work with REST and to follow the principles of REST.
3. They give us a simple, consistent, and organised structure to our URLs and to our code.
4. They also improve our *application security*. That's because we're able to very carefully regulate which operations are allowed on particular resources.
5. Most professional Rails developers use resourceful routes. And if that's your goal, is to become a real developer, then you'll need to use them too.

### Resourceful Routes

Rails gives us a convenient shortcut that we can use in our routes file in order to define resourceful routes.
```
# config/routes.rb
resources :subjects
resources :pages
resources :sections
```
All we have to do is **put resources, and then, as a symbol, the plural name of the resource that we want to work with**. So resources :subjects, resources :pages, and resources :sections. **That shortcut creates standard routes, which map to our CRUD actions using REST HTTP verbs**. Let's take a look at the routes that it defines for us.  

| **HTTP Verb**|**URL**| **Action**| **Description** |
|:------------:|:---------:||:--------:|:---------------:|
|GET|/subjects|index|show all items|
|GET|/subjects/:id|show|Show items with :id|
|GET|/subjects/new|new|Show new form|
|POST|/subjects|create|Create an item|
|GET|/subjects/:id/edit|edit|Show edit form for item with :id|
|PATCH|/subjects/:id|update|Update item with :id|
|GET|/subjects/:id/delete|delete|Show delete form for item with :id|
|DELETE|/subjects/:id|destroy|Delete item with :id

Notice that the controller name and the ID are still in the URL. However, the action names are mostly gone. And that's because the HTTP methods are largely going to determine the action.

A route is now a combination of the URL and that HTTP verb that goes with it. For example, **show, update, and destroy all have the exact same URL**. Notice that **it's the HTTP method, GET, PATCH, and DELETE, that makes the difference**. And the same thing is true for **index and create**. They have the **same URL**. It's the ***verb that makes the difference***. The ones that do **still show an action, new, edit, and delete, are really just conveniences for us to have a place to compose our changes in our browser**.

None of them are strictly necessary. If we knew what changes we wanted, we could just simply submit a post, patch, or delete with our changes. Imagine if you weren't using a browser, but instead you had a desktop application, or a mobile application that would request subjects/:id from the server as a GET request and then itself would provide some kind of editor for you to make changes inside that application. When you were done, you could submit those changes as a PATCH request, and you would never have called the edit action, just a GET followed by a PATCH.

But in a browser, assembling those changes is easiest if we present the user with a web form. These may feel a little bit odd at first, but if you look at it for a while, you'll see the logic of the structure. You can also type...
```
rails routes
```
from the command line to see the actual route mappings in an application. We'll try that a bit later. Now, I told you that resources was a shortcut for creating the resourceful routes we need. However, it doesn't include all eight of those actions by default. **The Rails team has decided not to include delete in the defaults**.
```
# config/routes.rb

resources :subjects do
  member do
   get :delete
  end
end
```
The reason is that it's optional whether we view information about a resource before we delete it. You may want to have a delete page which says, "You are about to delete this subject. "Are you sure?" Or you could just as easily have a delete link on the subject's show page. Some developers even put a JavaScript are you sure popup on that link. It's your choice, but delete is not included in the default actions defined by resources, so you would need to add it in the way that I've shown here.

#### You also do not have to define all the actions on a resource.
# Limiting Resourceful Routes
```
resources :admin_users, :expect => [:show]
resources :products, :only => [:index, :show]
```

It's not uncommon to have resources which do not allow some actions to be performed. You can use **:except and :only** in order to **list the actions which should be given routes**. And as we just saw with delete, **it's also possible to add additional resources**.

#### Adding Resourceful Routes
```
resource :subjects do
  member do
    get :delete
  end

  collection do
    get :export
  end
end

```
There's **member and collection**. **Member routes operate on a member of the resource**. In other words, they **expect to receive a record ID in the URL**. Edit and update are examples of built-in routes that are member routes.

**Collection routes operate on the resource as a whole**. In other words, **they do not expect to get a record ID in the URL**. Index is an obvious example of a built-in member route, but new and create also operate on the collection as a whole, not on an existing member. Before we leave this topic, I wanna mention that **it's common to mix resourceful routes and simple match routes**. Sometimes it's just **easier to map a URL to an action**, especially if we're not performing operations on a resource. For example, an About Us page on a website might not use the database and simply return static HTML.

However, whenever you're working with resources, try to use resourceful routes. So let's go into our application.
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

  # get ':controller(/:action(/:id))'



  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```
Let's add our first resourceful route. So we're gonna go into our routes file. Again, that's in config, routes. And I'm going to remove the match routes that it gave me by default.
```
Rails.application.routes.draw do

  root 'demo#index'


  get 'demo/hello'
  get 'demo/index'
  get 'demo/other_hello'
  get 'demo/lynda'

end
```

I don't want those in there anymore. And I'm actually gonna leave the root route up at the top of my file. That's always a good idea. And right below it, I'm gonna define the first route, which is resources and then :subjects.
```
Rails.application.routes.draw do

  root 'demo#index'

  resources :subjects do
    member do
      get :delete
    end
  end

  get 'demo/hello'
  get 'demo/index'
  get 'demo/other_hello'
  get 'demo/lynda'

  # get ':controller(/:action(/:id))'
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```
Now, we want all of the default routes that it gives us. There are gonna be seven of those. There's one that's missing that we also want so we're gonna add a code block here, and inside there we'll add member for our member routes and then another code block, and inside there we'll list the member routes that we want and it's going to be a get request for delete. So notice we could put get or we could put post at the front of it; we're putting get. So it's a get request for delete, and that will give me all eight of my CRUD actions that I have in my SubjectsController.
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

So we have index, show, new, create, edit, update, delete, and destroy, and **we have routes that are defined to all of those**. We can actually **see those routes if we go into the command line**, and from the root of our application we run
```
rails routes
```
and hit return, and it'll go through and it'll tell us all the routes that it knows about, every mapping that we have.
```
katymccann (master *) config $ rails routes
          Prefix Verb   URI Pattern                    Controller#Action
            root GET    /                              demo#index
  delete_subject GET    /subjects/:id/delete(.:format) subjects#delete
        subjects GET    /subjects(.:format)            subjects#index
                 POST   /subjects(.:format)            subjects#create
     new_subject GET    /subjects/new(.:format)        subjects#new
    edit_subject GET    /subjects/:id/edit(.:format)   subjects#edit
         subject GET    /subjects/:id(.:format)        subjects#show
                 PATCH  /subjects/:id(.:format)        subjects#update
                 PUT    /subjects/:id(.:format)        subjects#update
                 DELETE /subjects/:id(.:format)        subjects#destroy
      demo_hello GET    /demo/hello(.:format)          demo#hello
      demo_index GET    /demo/index(.:format)          demo#index
demo_other_hello GET    /demo/other_hello(.:format)    demo#other_hello
      demo_lynda GET    /demo/lynda(.:format)          demo#lynda
```
It can be very useful, especially when working with resourceful routes. So here we are, you see it has a verb here, listed to tell us what they are. And here's our subjects. Right, here's each one. Let's notice for example that we've got our index action here.

It's a GET request for subjects. If we want to update a subject, then we have a patch request for subjects/:id and that then goes to subjects#update. So that can be a helpful reminder if you forget exactly how these routes lay out. In addition to remembering these, Rails also gives us some resourceful URL helpers to help you out. Let's take a look at those in the next movie.
