# How does the controllers and views work and how do they allow us to output dynamic content?

![alt text](https://defendingdiaspora.files.wordpress.com/2013/07/mvc_archi1.png)

### How are templates are rendered?

Let's review the MVC diagram. Browser requests are routed to the correct controller and action. At that point, the controller takes over. And the controller's job is to control, control the flow through the application and to make choices about how to respond to the request. The controller could access our model and our databases during this process, but it doesn't have to.

### How does the controller decide which view should be used?

In the last chapter, we generated a demo controller which also created a demo directory in our views and added a file for our index action. This file is called a view template. It's a template because most of the time it's going to have dynamic content added to it.
- Add a new view template
- Inside views, inside demo, and next to index.html.erb now create hello.html.erb


## Now make these two templates different.

change index.html.erb
```
<h1>Hello World from index</h1>
```

in hello.html.erb

```
<h1>Hello World from hello</h1>
```

You now have two different templates, one called index and one called hello. But I can't access my hello template yet because I don't have a route for it.

 We need to have a route that matches. And currently, none of these route definitions match demo/hello. I could use a default route, and that would allow it to work. But we're not going to do that. We're going to stick with simple routes.

Got to route inside the config folder
```
Rails.application.routes.draw do

  root 'demo#index'

  get 'demo/index'
  get 'demo/hello'

end

```

 Add a demo/hello route. Now you have a simple match route which will match demo/hello and send it to the demo controller and the hello action.

### Now, let's take a look in the demo controller
```
class DemoController < ApplicationController
  def index
  end
end

```
You have an action for index but it doesn't specify what template it should be using.

And you'll see that we have an action for index but it doesn't specify what template it should be using.

It's Rails' sensible defaults that's telling it that the index action ought to match with the index template. And it works the other way around. Rails gets a request for hello but there's not a hello action defined in the controller. So there's no action code to perform, but it does use its sensible defaults to think that there might be a hello template, and therefore, it goes ahead and renders up our hello template.

### Now let's go ahead and add an action for hello, def hello end.

```
class DemoController < ApplicationController
  def index
  end

  def hello
  end
end
```

Now we have a placeholder for it. We've already seen that we don't need this action to be defined, but it is a good practice to have an action defined for each template.

#### The most common way to choose a template to render for an action
is to let this default behaviour kick in, to have nothing specified inside each action as to which template we're going to render.

#### If you don't want that default behavior
we can specify the template. You do that by calling the render method and then telling Rails what template we want to render.


## Render Template Syntax
```
render(:template => 'demo/hello')
```

Provide a hash where the key is :template, the symbol for template and the value is going to be the string demo/hello.

That tells it which template it ought to use. It's in the demo directory, and the template's name is hello.

## Short hand version
```
render('demo/hello')
```

And that will do the exact same thing. It'll know that we're talking about a template cause that's mostly what we want to render. And because we're in the demo controller, it will actually already be looking in the demo directory. That's the default directory for it to look in. We can simply provide the string for the name of the template that it ought to use. So if we tell it just to render hello,
```
  render('hello')
```
 it'll look in the demo directory because we're in the demo controller for a template called hello.

### Add some explicit render commands to index and hello.

```
class DemoController < ApplicationController
  def index
    render('index')
  end

  def hello
    render('hello')
  end
end
```

This is doing nothing different than what the defaults normally do.
