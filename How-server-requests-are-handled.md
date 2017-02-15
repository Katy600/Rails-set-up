# How server requests are handled

![alt text](https://recipe4developer.files.wordpress.com/2011/09/image.png)

#### A request originates with the browser
it goes to the Web server for processing. In our case, in development, that's going to be the Puma Web server, but it can also be the Apache, NGINX, or any other kind of Web server.

#### The Web server
is then going to look inside the project's public directory for a file whose path and name exactly match the browser's request. If it finds this file, it will return the file with the browser, and it will never access the Rails framework at all. Whenever the Web server doesn't find a matching file, it's going to ask the Rails framework to handle the request.

### The request
goes to Rails routing. Routing parses the URL, to determine which controller and action to use. It will execute the controller action, and then it will make a request to the model as needed, and finally render our view, just like we saw with the regular MVC architecture. It returns that view back to the Web server, which then passes it back to the browser.

### When you're configuring a production Web server
you will need to specify a document route. The *document route* is the public directory. Now, in development, Puma already knows that that's the document route, and uses it automatically, we don't have to specify it, but in production, you will need to configure your Web server to use that document route. As the name suggests, anything that goes in the public folder should be considered public, and that's true of most websites. What is different with Rails is that it tries to find a file in the public directory, and then if it can't find it, it asks the Rails application if it can handle the request.
