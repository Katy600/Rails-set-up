
# How to generate a controller and a view.

### MVC architecture diagram

- The browser sends a request to our controller code, and the controller then makes whatever decisions it needs, accesses the model and the database if it needs to, and then finally, when it's ready, sends its data on to the view, which constructs the presentation, and that's sent back to the browser as a webpage.

Make sure that you are in the root of your Rails application, and then from there, we're going to use the Rails command to generate template files.

```
rails generate
```
or

```
rails g
```

Choose a generator below.
- assets
- channel
- controller generators
- and so on

These are default template files that it will create for you, which are helpful, because then we can customize them. Controller is the one that we are interested in.

#### Now type ...
```
rails generate controller
```
.. and then we need to provide more information about what kind of controller we want to create.

### How to pass the controller name,
```
rails generate controller name-of-controller
```
Any arguments that follow this are going to be names of actions that are in this controller, the names of views.

#### For example, add an index view
```
rails generate controller name-of-controller index
```
If we had more, we could just put a space and keep listing additional views that we wanted to create as well.

It's creating different template files. It tells us where those are located, and you can see that the first one here, it says it created a file at app controllers slash demo controller dot RB. Inside this controller, the template file that it created for us is just basically a Ruby class. You have a class definition, the name is demo controller, and it inherits from application controller.

```
class DemoController < ApplicationController
  def index
  end
end

```

 This class has a Ruby method in it that's called index, and when a Ruby method is in a controller, it's also called an action. It's the action that we're asking this controller to perform. This action doesn't contain any code at the moment, but it does render a view. It also renders a default view layout.

For now, we're just going to turn the layouts off.
```
class DemoController < ApplicationController

  layout false

  def index
  end
end
```

### What else did it create?

```
Running via Spring preloader in process 46761
Expected string default value for '--jbuilder'; got true (boolean)
Expected string default value for '--helper'; got true (boolean)
Expected string default value for '--assets'; got true (boolean)
      create  app/controllers/demo_controller.rb
       route  get 'demo/index'
      invoke  erb
      create    app/views/demo
      create    app/views/demo/index.html.erb
      invoke  test_unit
      create    test/controllers/demo_controller_test.rb
      invoke  helper
      create    app/helpers/demo_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/demo.coffee
      invoke    scss
      create      app/assets/stylesheets/demo.scss

```
Inside views, inside the demo directory, there is now a file called index dot HTML dot ERB. Notice how the folder name corresponds to the controller name, demo, and that the view template name corresponds to our action, index.

This is an example of Rails conventions, and sensible defaults at work. We can use different names, but by default, Rails will assume that it can find templates for the demo controller inside the views demo, and that the template for the index action will be called index. Inside the view file, you will see that it contains some basic HTML.

```
<h1>Demo#index</h1>
<p>Find me in app/views/demo/index.html.erb</p>

```

I'm going to remove this for now, and let's just add here the obligatory hello world. Let's save the file, and we can close it.

```
<h1> Hello World </h1>
```

#### Root files
The routes file lives in the config directory

```
Rails.application.routes.draw do
  get 'demo/index'

  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end

```
You'll see routes dot RB. This is a file that it was referencing, and what it did for us was it added this route right here. We need to have the route declared here to be able to wire things up.

So, having demo index here is going to be able to enable the controller and the view.

# Lets try looking at our page.

- Make sure that we have our Web server running, s
- Load up localhost colon 3000.
- put slash demo slash index, and you see that I now get hello world.

So now, we've been able to successfully create a controller, and a view, and we've been able to make that loop from the browser, to the controller, to the view, and back to the browser. Before we move on, let's talk a bit more about how server requests are handled.
