# How to redirect actions from the controller.

![alt text](https://defendingdiaspora.files.wordpress.com/2013/07/mvc_archi1.png)˜

Controllers can respond to requests by rendering a view back to the browser. But the diagram that we've been looking at is deceptive because it appears that the controller's final step must be to render a view. But it can also make a different choice, and that is **to perform a redirect instead**.

### Redirects
Redirects send a request to a different controller and action. Imagine this scenario; a user requests a page which is inside a password protected area, so the first thing the controller's gotta do is check to see if the user is logged in or not.

If the user is logged in, then it will let them have the page that they wanted, but if not, then the controller wants them to log in first, and so it redirects them to a login page instead. We could use render to render that login page, but that would just be rendering the login template.
What we really want is to **send the user to the new URL for the login page and run any controller action code that's associated with it and then render the template.**
It's an important difference. **A redirect is not just a Rails concept, it's part of the HTTP specification that makes the web work**, and Rails puts them to good use.

### What happens when you redirect?
Rails returns a status code to the browser which looks like this.

```
  HTTP/1.1 302 Found
  Location: http://localhost:3000/demo/hello
  ```

 The location is the URL that we'd like to redirect the browser to. So when the browser gets that message back from the web server, it says, oh **302, you're trying to redirect me to a different page***. The browser then immediately requests that new page,

The browser is making a brand new request to the web server which then goes back through the Rails routing into the controller and action.

### How do we write a redirect in Rails?

```
class DemoController < ApplicationController

layout false

def index
  render('hello')
end

def hello
  render('index')
end

def other_hello
end

```

I'm gonna create a new action here right after hello. I'm gonna call it def other_hello, and inside it I'm gonna put a redirect. But before I do that, let's remember to jump over to our routes file and make sure that other hello will work.

```
Rails.application.routes.draw do

  root 'demo#index'
  get 'demo/hello'
  get 'demo/index'
  get 'demo/other_hello'

end

```

So let's add another line here, other_hello. Now other hello will route to my action other hello. Okay, so now let's have other hello do a redirect, and we'll just have it redirect to the index action.

```
def other_hello
  redirect_to(:controller => 'demo', :action => 'index')
end
```



 So the way we do that in Rails is with redirect_to, and then in parentheses we tell it where we want it to redirect. We can say controller, and then the controller, which will be demo, and comma action, and the action will be index.

Now because we're already in the demo controller, that's going to be assumed by default.

```
def other_hello
  redirect_to(:action => 'index')
end
```

If we don't specify controller, it's going to use the current controller as its default. So all we actually have to have here is the action redirect to the action index.  There we are, hello world from hello.

Most of the time you're going to be redirecting to a different controller and action inside your own application. But it doesn't have to be, you can redirect to anywhere, including an external URL. For example, let's make another one here called lynda, and let's write a redirect that's going to go to http://lynda.com.

```
def lynda
    redirect_to('http://lynda.com')
end
```

Now remember I have to also add that to my routes file so that that will work as well.
```
get 'demo/lynda'
```
The first request did, I sent back a redirect, and then the second request just went to the lynda.com website. So a controller is able to render a template, or redirect, to a new location. And a redirect is actually just telling the browser that it should quickly go to a new URL.
