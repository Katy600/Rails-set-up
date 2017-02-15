# There are several kinds of route definitions.
- the simple route, also sometimes called the match route
- the default route
- the root route.
- the resourceful routes,

## There are two primary types of HTTP requests, get and post.

Get is what happens when you type a URL into the browser, or when you click a link, post is typically what happens when you post a web form, or submit form data to the Web server.

### The routes file is inside the config directory, and it's called routes dot RB.
```
Rails.application.routes.draw do
  get 'demo/index'
end
```
You can see right now I just have one route definition, get, and then the string demo slash index. This is the routing rule that allows demo slash index to be correctly routed to the demo controller, and the index action.

### Simple Match route
```
get "demo/index"
```
### Longer version
```
match "demo/index", :to => "demo#index", via: => :get
```

If you were using only simple match routes, then it means that every single page in your application would have to have a static string associated with it, so it would know where to route it, and you could do it that way, but it's not very flexible.
### Default route
It would be better if we could instead write a routing rule which would handle many cases that could parse the string into the right components, and that's what the default route allows us to do.

### The default route structure
```
:controller/:action/:id
```
is controller, then a forward slash, then the action, and then a forward slash, and then the ID.

```
    GET/students/edit/52
            |
            |
            V
    StudentController, edit action, id = 52
```
That means that a request that comes in for get slash students slash edit slash 52, using the default route, would get routed to the students controller, the edit action, then the value of ID would be set to 52.

### How you would right this.
```
get ':controller(:action(:id))'

match ':controller(:action(:id))
    :via => :get
```
It is like a simple match route, but inside the string for it, you would put special variables, symbols for the controller, the action, and the ID. Each one of those serves as a placeholder. The way that you would write this default route is by using a simple match route, but inside the string for it, you would put special variables, symbols for the controller, the action, and the ID. Each one of those serves as a placeholder. It works almost like a regular expression, where we try and match a portion of the URL, and then we capture the value that's there if it matches, and assign it to the value of controller. We do the same thing for action, and also for ID. You'll notice that action and ID also have parentheses around them.
That's because those become optional parts. The default route allows them to be omitted, and have the route still work, so we can specify just a controller, a controller and an action, or a controller, action, and an ID.
##### *The controller always has to be specified, because we need to know where to send it.*

###### The action though, has a default value which is index, so if nothing has been specified, the controller will map to its index action by default.

# Root route
This is essentially the home page of your website. It's all well and good to match routes based on what comes after the domain name, but what do you want it to be if there is nothing to match at all? Where should that go? That's our root route. We could write that out in the long form by using match,
```
match '/', :to => "demo#index", :via => :get
```


 but the shorter and better way that you will see most people do it is
```
route 'demo#index'
```

just to say root, and then a string telling it which controller and action should we route it to. Again, using that pound sign in between them.

Let's add that in as well, right here at the top.
```
Rails.application.routes.draw do

  route 'demo#index'

  get 'demo/index'

end

```
Let's add route, and then the string, and demo, with a pound sign, index. Notice it's the pound sign, it's not a forward slash like it was here. This is using this to match it, this is telling it which controller and which action, so they are slightly different.

It doesn't go to that default welcome to Rails page anymore, so we've successfully added our root route.
