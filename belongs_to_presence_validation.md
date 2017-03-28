# Belongs to presence validation

### Belongs to presence validation is a change in Rails 5.0, It didn't exist before that.
Prior to Rails 5.0 **an object that had a belongs to relationship could be saved to the database even if it didn't have a parent object associated with it**. That is **the belongs to relationship was not required To be present**. ***Now***, starting in Rails 5, **the belongs to relationship is not optional by default, what happens behind the scenes is that when we declare a belongs to, active record adds a validation on the object for the related objects presence**...

The parent object. So if we have our page that means, that the **page won't save to the database. Unless we have a subject associated with it**. Now we're gonna talk more about validations later. But all that you really need to know for now, is that **a validation is there to check and make sure that the data is correct before allowing the record to be saved to the database**. So, **if the subject is not present, then the page can't be saved**. Now again, this is set to not optional by default. You can disable this option. And there's two ways you can do it.

One, you can do it as a configuration change. And you can do it for your entire application. And that's sometimes done for legacy Rails 4.0 apps while they're being upgraded to Rails 5. But, I don't recommend doing that as a practice because that configuration's probably going to go away in the future, and this is the way it's going to be for everyone. So instead, if you want to disable it, you need to do it on a case by case basis. By setting optional, equal to true. Let me demonstrate. Let's go into our Rails console.

And from inside the Rails console we're going to bring up a page. We'll create a new page, page dot new. And we give it a name...
```
2.2.3 :001 > page = Page.new(:name => "No Subject Assigned")
 => #<Page id: nil, subject_id: nil, name: "No Subject Assigned", permalink: nil, position: nil, visible: false, created_at: nil, updated_at: nil>
2.2.3 :002 > page.save
   (19.6ms)  BEGIN
   (6.4ms)  ROLLBACK
 => false
```

And the name, I'm gonna call it no subject assigned. Okay so there's no subject assigned to this. Now let's just try saving it. Page dot save. Now remember, save will return true, or false. Letting me know whether it's saved, and I should see some SQL on my screen, in my console letting me know if it actually saved it. It did a begin and then **I get a rollback and false...**

**It did not save it**. Again we'll talk more about validations later, but for now, let me just show you that you can call page dot errors, dot full underscore messages.
```
2.2.3 :003 > page.errors.full_messages
 => ["Subject must exist"]
2.2.3 :004 >
```
And you can see what the message is. Subject must exist. That's the error that kept us from saving to the data base, so there's two solutions to this. One, we could assign the page to a subject, like we did in the last movie. And then the page would be saved automatically. Or we could set a subject ID here, and then save the record.

And it would save correctly... Or, the other possibility is that we could go into our model, and after belongs to subject, right now,
```
class Page < ApplicationRecord

  belongs_to :subject {:optional => false}

end

```
the default in Rails is optional, false it's not optional, we would just need to change that to be true.

```
class Page < ApplicationRecord

  belongs_to :subject {:optional => true}

end

```
So, **belongs to subject optional true, and that would mean that we could save pages to our database. Without having a parent object**. A subject, that it belongs to. You should be careful about when you disable this feature though, it's there for your protection.

**It's to keep you from putting orphaned records in your database**. To have pages in there that don't belong to any subject. And it might never then be seen or accessed, in anyway... And just clutter things up. Now that's not to say that you can't still have orphaned records, for example if you remember, we already have an orphaned record in there. We have page ID equals two, and its subject is equal to nil... now that's not a valid record. It's not something that should have been able to save to the database. If we tried to save it directly it wouldn't have worked. The reason it's there is because we used delete, and we used delete to delete it from the set of records and that had the effect of setting the subject ID to nil...

and it did not go through the validations at the same time that it did it. That behavior may change in the future as well. For now, the important change is that optional has been set to false, by default. Most of the time it probably won't make a difference. But it is something that is important to be aware of.
