
## What is REST?
**REST is short for Representational State Transfer, and REST is a design concept which says that our application should not focus on performing procedures, but instead on performing state transformations upon resources**.

It's not easy to see how that's different from what we're already doing because the Rails MVC pattern already encourages us to break up our code by model and controllers for those models. Each model is already being treated as a resource. In fact, it may be helpful for you to think of model whenever you hear me say resource. The reason REST was quickly adopted by the Rails community is that it fit easily with what we were already doing. But if you ever coded webpages without using a web framework, maybe writing basic PHP, then you'll have a better understanding of the difference.

In most websites, you write a webpage in which the user triggers an action, that is, performs a procedure. And that procedure could be anything. It could be a long page of unstructured and unorganized code which updates many different database tables at a time. REST asks us to be more organized and to think about our data as resources, and then to think about making changes to those resources. We're already doing that by having distinct models and writing distinct CRUD actions in our controllers to change those models.

I know that's still abstract. I think it will become clearer once we have some examples. To start using REST, there are four changes to our way of thinking and to our code that we'll need to make.

#### 1. We need to organize our code into resources.
We've got a distinct table in our database for each resource. Each table has a model, and in the last movie, we just talked about having one controller per model. So we've got that covered.

#### 2. We're going to need to learn some HTTP verbs for working with those resources. And talk about how our CRUD actions map to each one.

#### 3. Learn about a new URL syntax for our controller actions, and that's going to influence the way that we write our Rails routes.

#### 4. Modify our existing links and our forms to use that new syntax.
And we're going to have some help there using some resourceful URL helpers which we'll learn about later in this chapter.

# HTTP verbs.

| **Verbs** |...........**Objective**..........| **Usage** | **Multiple Requests**| **Cache/Bookmark** |
|:---------:| :-------------------:|:---------:|:--------------------:|:------------------:|
|GET        |Retrieve items from resource| links| yes| yes|
|POST       |Create new items in resource| forms |no | no |
|PATCH      |Update existing items in resource|forms | yes | no
|DELETE     |Delete existing items in resource |forms| yes| no

There are four verbs: GET, POST, PATCH and DELETE. GET and POST you recognise as basic HTTP operations where you get a URL or you post form data.

**GET is used for most links**. *We're getting information back*. Notice in the far right column that ***GET requests are the only pages we want to let the serve cache for faster delivery or allow our users to bookmark***. In most web applications, **you would use POST for all other forms**. For **forms which create, update and delete items**. But **in REST, POST is only used to create a new item**. When we want to **update an existing item, we use PATCH**, and when we want to **delete an item, we use DELETE***. ***Why not just use POST?*** Because we want to *reserve POST for an operation on our resource that should really only be run one time such as adding a new item*.

You wouldn't want to accidentally add the item to the database more than once. That's why when you reload a posted form page in your browser, you get a message that says, are you sure that you want to repost this form data? **PATCH and DELETE also make changes to our resource, but unlike POST, a web browser can run them multiple times without any problem**. For example, if I change a user's email address to a new address, it's not a big deal if that request comes in a second time, and it tells it to update it to the new address again. Get familiar with these verbs because remembering them will make learning REST much easier.

###How do these those verbs map to our basic CRUD actions?

| **Action** | **HTTP Verb**| **Description** |
|:----------:|:------------:|:---------------:|
|Index| GET|show all items|
|Show|GET|Show items with :id|
|New|GET|Show new form|
|Create|POST|Create an item|
|Edit|GET|Show edit form for item with :id|
|Update|PATCH|Update item with :id|
|Delete|GET|Show delete form for item with :id|
|Destroy|DELETE|Delete item with :id

In the far left column, I have a list of the CRUD actions. Notice that I've reorganised them slightly though, and I've moved index and show, the two read actions up to the top. **Index and show both use the verb GET**. And that makes sense because we're **getting information**, we're getting **all items**, and then we're getting a specific item. We also use **GET for new, edit and delete because all three of those are displaying a web form**. **They're not making changes to our resource**, they're ***just getting information back about it***.

#### When we submit the form, we're actually making changes.
And for that we have three different verbs. For **create we have POST**. For **update we have PATCH**. And for **destroy, we have DELETE**. Hopefully now you're starting to see how we're beginning to **think about our data as a resource and that our CRUD actions are going to be performing state transformations on that resource**. There's one more footnote I want to make about the way that REST is implemented in Rails. Even though HTTP and most web servers support our four REST verbs, different HTML versions have varying levels of support for REST, therefore, Rails uses a work around so that you can use REST universally.

It **fakes a true REST request by adding a hidden field called _method to the form, and then it uses the POST as the method for the form.**

```
<from method ="PATCH" action="/subject/123">
...
</form>

<from method ="PATCH" action="/subject/123">
  <input type="hidden" name="_method" value="patch" />
  ...
  </form>
```
 This hidden field indicates to Rails whether it's meant to actually be a PATCH or DELETE. So the form uses POST, but when Rails gets the request and sees that hidden field, it knows to treat it like the REST method you've specified. This is a minor technical point. In reality, you won't worry about it much. When we learn to work with forms later, Rails offers us form helpers which will take care of making these adjustments for you.

But if you see it in your HTML page source, you'll know why it's there. Now that we know about the different HTTP verbs and how they match the basic CRUD actions, let's learn about the changes to our routes and the URL syntax.
