
# App directory
 This is where most of your application code is going to live. Inside, there's a number of other directories.
### The most important folders:
   - Models
   - Views
   - Controllers

That's our MVC architecture, that we talked about previously. You're going to put most of your code in those places.
#### Other useful folders

###### Helpers.

These are really view helpers, they help us to write code in our views.

##### Assets,

This is where you put your JavaScript, stylesheets and some of our fundamental images.

##### Not so useful folders
- Mailers:
Where you can have your application send email,

- Jobs:
For writing tasks that your application is going to do. Let's say, something it's going to do every night, at midnight. You could put a job in here for that.

- Channels:
 is used for Action Cable. That's a new feature of Rails 5, and you would put all of your Action Cable code inside channels.

- Config directory: This is where you configure different aspects of our application. There are a lot of sensible defaults that are already in there, and you just need to override those and change them, whenever we want to make a configuration change.

- db directory:
This is short for database and this is where all of our database files are going to go.

- Gemfile:
 This specifies what RubyGems our application is going to load up.

- Gemfile lock:
 is related to the Gemfile but that's not a file you need to manage. Rails is going to handle that for us.You can add gems to the Gemfile, whenever you want to configure them.

- log directory:
 That's where log files are going to be stored, as we work with our application. That's useful for tracking down errors and finding out what happened in an application.

- public directory: This is where public files that don't need to be served up by the application can live.

- Test directory: which is where, if we wrote test code, using Test::Unit or Rspec, you can put that code inside the test directory.

- lib directory and a vendor directory:
 These are other places to put code that might help you out. Most third-party code is now bundled up as a gem, so it will live in RubyGems, but if not, if you had some third-party Ruby code, you could put it in the vendor directory. Or, if you had code of your own that you wanted to reuse from project to project, you could put that in the lib directory.
