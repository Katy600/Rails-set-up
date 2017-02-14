# How to Access a Project in the Browser.

#### First Step
Start up a web server.

A web server is what's going to receive requests from a browser and pass them off to our Rails application. When we launch our code in production for the whole world to use, we're going to want to use a robust web server, something that can handle hundreds of requests at a time and has lots of features that we can use. But, for development we don't need all of that.

We can use a very simple light-weight web server that's very fast, and that's what we're going to be using here. By default, Rails 5 is going to want us to use the Puma web server, so we don't need to do anything. That's already built-in; it's going to load up Puma for us.

### How to start our default web server.

Go to your command line and navigate to the root of our Rails project, and then we type...
```
rails server
```
or

```
rails s
```
That will boot up Puma so that it's listening for web requests.

As soon as it says, "Listening," that means that Puma is ready to accept requests.


### How to stop the web server.
Just type...

```
CTRL + c
```

### How to restart the web server.
Simply stop it and then start it again.

Most you can just leave it running, even if you're adding code to your application.

### How to visit the site in the brower.

Go to http://localhost:3000 or for any reason localhost isn't set up on your machine, you can also use the IP address 127.0.0.1.
By default, most web requests and most web servers work with port 80. Here, Puma's going to be listening for requests on port 3000, so we must specify port 3000 in the URL.

 
