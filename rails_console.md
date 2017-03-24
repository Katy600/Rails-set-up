 The way that you launch the console, is from the command line...

```
rails console development

rails console

rails c
```


 , you type rails, then console, and then the environment that you want to enter. That's going to be development when I'm developing on my machine. But it could also be production, or test, or maybe you have another environment for staging or QA. By default, it's going to assume that it's the development environment. So, most of the time in development, you're just going to type rails console, or, there's an even shorter shortcut which is just rails c.

And that's what I find myself typing the most often. If you have prior experience with Ruby, then you may be familiar with the Ruby Console. That is, irb, or Interactive ruby. If you just type irb from your command line, and you have Ruby installed, you'll then get an Interactive Ruby. It works a lot like a calculator, one plus one, returns two. If I have a string like, hello, I call upcase and reverse on it, then I'll get back a upcased, reversed version of that string. Now this is just Ruby, by itself, it's not Ruby on Rails.

Exit out of that. Now, I'm going to enter the Rails Console.
```
katymccann (master *) simple_cms $ rails console
Running via Spring preloader in process 46778
Loading development environment (Rails 5.0.1)
2.2.3 :001 >

```
It launches, and you can see here, it tells me that it's loading the development environment, and it's loading Rails five o. Notice here, that the prompt looks very similar to what we had up here, irb and irb. That's because what we're loading up is, Interactive Ruby. But it's Interactive Ruby with our Rails environment loaded as well.

```
katymccann (master *) simple_cms $ rails console
Running via Spring preloader in process 46778
Loading development environment (Rails 5.0.1)
2.2.3 :001 > subject = Subject.new
 => #<Subject id: nil, name: nil, position: nil, visible: false, created_at: nil, updated_at: nil>
2.2.3 :002 > subject.name = "Test"
 => "Test"
2.2.3 :003 > subject.name
 => "Test"
2.2.3 :004 >
```
So that we have access to not just Ruby, but also all of our rails, models and other code. So we can still do one plus one, we can do hello, upcase, reverse. But we can also do subject equals subject new. And it will use out subject model that's defined in our application, in order to create a new instance of this class. This is a great way to work with models and data. Subject dot name equals, call it test, and subject dot name, return the value test to us.

It's a good way to test and try things out, and poke and prod at your models. And you don't need to have a controller, or a view, or bring it up in a browser, in order to interact with your models. You get a much more direct relationship with them. And that can be really helpful. To remove those other aspects of it, to get right in with your models, and see what's going on. Now, if we were to save this subject to the database, it would save it, and it would save it to our development database. Just because we're inside our console, doesn't mean that we are interacting with a different database, than if we were coming at it with a controller and a view.

That's important to understand. It's especially important, if you go into the console for your production environment. The live environment that the public is using. Just because we're using the command line, doesn't mean that you're not making changes to the live production database. So tread carefully. We're gonna be using the console throughout the remainder of this chapter, so that we can focus exclusively on how our models behave.
