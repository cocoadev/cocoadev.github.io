---
layout: page
title: BestSavingMethod
---

Hello, I have a question to ask.

I have an array of dictionaries as my root source of data that is used throughout my app.  This works perfectly.  I don't use any custom objects (just a bunch of nested dictionaries and arrays) and on quit and startup I load/save with NSCoder.  This works too, but��� I am potentially dealing with a large amount of data.  My question is simply this, what would the benefit to using CoreData and an SQLite method - and would it require a major restructuring, or could I simply tell it to manage my array of dictionaries?  Thanks a bunch.

----

Advantage - You get a bunch of neat stuff for free. Disadvantage - It's 10.4 or above only and not many people have upgraded yet. As to major restructuring? Yeah, you'll probably have to change a lot of things if you used a lot of glue code, rather than CocoaBindings. You'll also have to use a data model for Core Data, of course. 

If your array of dictionaries represents a collection of people, for instance, you would create a "Person" entity, define its properties (like lastName, firstName, etc.) and for every "Person" created, they'd all be stored in the database (along with other objects such as "Task"). In other words, you'd have various "Person" and "Task" instances in your database. Your fetch requests and array controllers (via use of the "managedObjectContext" binding) would ask for all the people and tasks, etc. The neat thing is, a managed object (NSManagedObject) uses KVC (KeyValueCoding) so you can say     [thisPerson setValue:@"Smith" forKey:@"lastName"];. To use  CocoaBindings to show all the Person objects in a table by first name and last name, for example, you drag a table and an array controller into a window in Interface Builder. The array controller's represented object would be an "Entity" rather than a "Class". You give it "Person" for the entity. Then you bind its managedObjectContext binding (to your app delegate or your persistent document's managedObjectContext) and it'll automagically give you a list of all Person entities in your store. (I leave the exercise of binding the table columns to the array controller to you.) Do the same thing for tasks.

Another cool thing is the relationships part of it all. If a Task can be assigned to a Person, you can define this relationship easily in the model. If you create a relationship with an 'inverse' (Person can have many tasks, tasks can have only one person, for example), assigning a person to a task automatically assigns that task to the person. CoreData maintains both sides of the connection if you tell it to.

----

Thanks for the response.  Then, looking at this, is it really worth it - considering I already have everything working with glue code?  If the only advantage is free stuff, then why bother - even if it would be easy to redo, I would be limiting to 10.4+.  Thanks again, and let me know if I'm wrong in choosing not to switch to CoreData.

----

That's entirely for you to decide. I don't know your app, your target audience, or your personal preferences. ;-) Just because CoreData exists doesn't mean you're wrong for not using it. In fact there are many situations where CoreData (and even Bindings) simply *won't* work. For instance, you don't have to use CoreData to make use of Spotlight (I think a few people have been confused by this issue in the past, which is why I'm mentioning it now). You can still write a spotlight importer for your existing app.

