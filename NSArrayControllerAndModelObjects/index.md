---
layout: page
title: NSArrayControllerAndModelObjects
---

**Setting and retrieving data from a document**

I want to use an NSArrayController in a document-based application where NSTableView displays array object data in other fields in the window in a sort of Master-Detail fashion (very similar to the Enhanced Converter example on the Apple site).

I want the controller to manipulate an array of objects that's already present in the document object so that I can use the document's archival methods to save/load the data to/from a disk file.

How do I make the controller control my "combatants" array?  Is it as simple as:

1.  Doing this in code (in the init method for the document?):  [combatantController setValue:combatants forKey:@"contentArray"];

or 

2.  Doing this in IB:  Set the Bindings for Content Array to these values:

Bind to - File's Owner (iDMDocument)

Model Key Path - combatants

----

Keep the NSArrayController; set and retrieve the array whenever you need to save/open a document. You can do that using the inherited NSObjectController     content and     setContent: methods.

Or instantiate an NSMutableArray object in IB and linking the     content outlet of the NSArrayController to that array. You can then create an outlet in the document object that links to that same array.

-- RyanBates

----

I'm unclear on how I would set/retrieve the array on-the-fly.  How can I take an already established NSArrayController and change the data it points to?  

Or should I copy all the data from the document's array into the controller somehow on open, and then when I want to save, copy the data from the controller back into the document's array for archiving?  Then how do I actually access the individual items in the controller's array?  (Or the controller's array for that matter?)

----

My first suggestion was to call     setContent: when loading data:

    
- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    [arrayController setContent:[NSKeyedUnarchiver unarchiveObjectWithData:data]];
    return YES;
}


And calling the     content method when saving the data *(oops! fixed)*:

    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    return [NSKeyedArchiver archivedDataWithRootObject:[arrayController content]];
}


Calling     setContent: may not update the UI properly.

If you ever need to access an individual object from the array, you could simply use the     content method again.

    
object = arrayController content] objectAtIndex:i];


It should basically act the same as if you instantiated the array in IB and used that. However, I would try this way first because I try to avoid instantiating data-type objects through IB; however, with bindings brought into the picture, IB manages data as well as the interface.

-- [[RyanBates

----

What if I am saving other variables at the same time?  I was using:

    
return [NSArchiver archivedDataWithRootObject:self]; 


and assuming that all my document's data is in the document itself (and therefore gets archived with :self, above.  Now, your example only archives the data in the arrray.  What if I want to archive both the arraycontroller's array data and my own other data?  Should I write a method to copy the controller's data to my variables and then archive, and then write a method to copy it all back to the array controller after the load?  Or, is there a way to archive everything in the document's variables AND the arraycontroller's array at the same time?

----

NSDocument is a controller type object, meaning you probably don't want to archive it. Create an NSMutableDictionary and add the controller's array and any other objects you want to save. Then archive the dictionary. For example:

    
NSMutableDictionary *dict = [NSMutableDictionary dictionary];

[dict setObject:[arrayController content] forKey:@"contentArray"];
[dict setObject:myString forKey:@"myString"];
// etc...

return [NSKeyedArchiver archivedDataWithRootObject:dict];


Don't forget that when unarchiving the data object, it will be an NSDictionary.

Another option is using an NSMutableData object and setting up the archive using that.

----

So after I unarchive the saved NSDictionary, I can rip it apart and set my NSArrayController's content as well as other variables from the just-unarchived dictionary.

----

**Parent-child data and NSArrayController**

I have two class that form a parent-child relationship. The Parent class contains a mutable array where the array contains only Child objects.  I have methods in the Parent class that can add new Child class to the array.

For the UI, I have an NSArrayController that holds Parent objects and displays them in a tableview. That works perfectly. I have another NSArrayController, childArrayController, that binds the first controller, parentArrayController, selection key. There is a key in the Parent object called CHILDREN that returns a copy of the array from the Parent object.

The problem is that if I add child objects using the methods in Parent, the childArrayController doesn't recognize the new object in the array.

How should be I be coding this situation?

----

Instead of using a copy of the array, you should just use the array itself, and set childArrayController not to allow changes to it. That might or might not solve your problems -- bindings are tricky. The alternatives I can think of are to use the methods of NSArrayController and your childArrayController to add objects to the Parent (ugh!) or to call -reloadData on your table view after each change (much easier).

--JediKnil

----

You'll need to use KeyValueCoding's indexed accessor methods (which you can see on KeyValueCoding) instead of your own custom methods for modifying the children of a parent.  In this case, I'd rename     CHILDREN to     children, and one of the methods to implement is     -[ParentObject insertObject:inChildrenAtIndex:].

Go to http://homepage.mac.com/mmalc/CocoaExamples/controllers.html and search the page for "Programmatic modifications to arrays not noticed by table view".  It's pretty much the same problem, with the same solution.

