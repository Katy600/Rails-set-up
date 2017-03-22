# View templates

So far, view templates have been composed of only HTML, we could use just HTML, but that won't be dynamic or data-based driven.

It would be much better if we could embed Ruby code into our templates so would could then put dynamic information into them. To do that, we're going to use ERB. **ERB is short for "Embedded Ruby." It's an eRuby templating system to allow us to embed Ruby inside our HTML pages.**

hello.html.erb. What that means is we have a template named hello, we're going to process it with ERB, and when we're done, the final output format will be in HTML.

The first step in being able to embed Ruby code, is to make sure we have the correct file extension. If we don't have that .erb at the end, then Rails won't use ERB to process the file. The second step is to embed our code in the template. And we do that using ERB tags. There are two versions of it. Each of them have <% and then our code, followed by %> at the end. Those are the bracketing tags that surround our code.

```
  <% code %>
  <%= code %>
```

The difference is the first one does not have an =, the second one adds an = to it. **The first one simply processes and executes Ruby code, the second will execute the Ruby code, then output whatever result that code returns into our template.** So the first one does not output and the second one does.


Let's put <% 1 + 1 and then we'll do our closing tag %>. Let's save it.
```
<% 1 + 1 %>
```

Make sure your web server is still running. Then let's go to our browser and let's load up localhost:3000/demo/hello. Now I get back Hello world from hello! Which let's me know that it did switch to the correct template, but I did not get any output. It did not actually put the number two there. And that's because I used the ERB templates which calculate the value, but they don't output it, they don't do anything. So it did add 1 + 1 together, but then it didn't do anything with the result.

If I add an = in front of it, now it will both calculate it, and output it to the template. So let's save it, we'll come back over and reload the page and now we see 2 being output to the template,

<% target = and I'll use "world" %> close those tags, Now I'm going to use output tags, and I'll output the string <%= "Hello #{target}"%> and we'll use closing strings.


Now this is perfectly valid Ruby, and it's perfectly valid ERB. But it's not going to do what you might expect it to do. Go ahead and I'll also just put a line return in here so it will be separate, and let's reload our page. Now nothing was output here at all. You see that? Nothing was output and you may think oh, well, that's because we don't have = tags. Let's put an = at the end of it, and let's try this now. Let's reload the page again. Now it output 3. But that's not what we would expect this to do. In Ruby, 3 times do should render up a value to n.

It should be 0, then it should be 1, then it should be 2. So each time through it should output those. So you should expect to see 0, then 1, then 2. But instead we got 3. Well if you notice over here in our console, we did get 0, 1, and 2. That's what happens. Puts will output to Ruby's standard out, which outputs to the console. That's where it goes to. It does not output it to the template. The return value of this, though, is 3.
```
<%= 3.times do |n| %>
    <%= n %><br />
  <% end %>

```

That's the return value at the end, and so what gets returned when we actually use = what gets returned is 3. So just be careful about that. It's about the value of what this returns, if we want to actually have this output, you'd want to write it this way in Rails. Let's take our end tags like this, and then percent equals <%=, and then let's close those tags %>. And if we really wanted this to work like the Ruby version where each one is on a different line with puts, we'd probably want to have a br tag there as well.

So let's save that, come back, reload our page, and now we get what we would expect. We get 0, 1, and 2. So hopefully you get the basic idea. We surround Ruby code with our basic ERB tags, we surround Ruby code which we also want to output a value, with ERB tags that include =.
