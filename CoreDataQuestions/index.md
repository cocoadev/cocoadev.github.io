---
layout: page
title: CoreDataQuestions
---



Questions relating to CoreData.
Also see: CoreDataInheritanceIssues

And now that we've got the initial round out of the way, it is time for some ambitious soul (i.e. not me) to refactor these answers to the appropriate places, ne? http://goo.gl/OeSCu

----

When you dial into an array through a key path, we have operators to do aggregation, for example like company.projects.@count or produc ts.@avg.prices .  When I try to use these on the MutableSets returned by CoreData relations, it can't give me a count, and the log reports that the @count key path is not supported.  What key path can I use if I want to know the number of objects on the other side of a to-many relation?  Or am I completely missing something?  It seems that a FetchedProperty might do this, but XCode rejects all of the predicates I try to build to do this.

*I got this to work by subclassing the "owner" of the to-many relation thus: *

    

+(void)initialize
{
	[MQUsage setKeys:[NSArray arrayWithObject:@"cues"]
             triggerChangeNotificationsForDependentKey:@"cueCount"];
       /* "cues" is the key to my related set */
	
	[super initialize];
		
}

-(unsigned int)cueCount
{
	return self valueForKey:@"cues"] count];
}



It seems like there should be a simple binding key for getting the cardinality of a to-many without having to write mehtods, but "_NSFaultingMutableSet" doesn't appear to be KVO compliant for -count.

----

Does anyone have ideas how one should define new Persistent Store Types? For example, if I want to enable my [[CoreData application to save and fetch the data from MySQL database, how should I implement this? I wasn't able to find any "NSPersistentStore" class to override, so it seems kinda impossible at this momemt. Any ideas? -nzhuk 

*There's no public API for implementing your own store type in Core Data 1.0. The most you could do is use Core Data for change tracking and do all the reading/writing yourself.* -ScottStevenson

----

It looks like the only types CoreData can handle natively are numbers, dates, strings, and binary. How are other types to be handled?

*Binary = every type you'll ever need.* :)

I guess you have to write KeyValueCoding accessors to convert between your native format and NSData, and use setPrimitiveValue:forKey: to let CoreData persist it? This strikes me as very inefficient.

I found Apple's recommended solution at http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdNSAttributes.html.

----

If I'm building a Core Data Document-based Application how do I make sure version 2 of my app will be able to open up version 1 documents? - MikeZornek

*See this post from cocoa-dev:*
http://lists.apple.com/archives/cocoa-dev/2005/May/msg00640.html

See also: http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdVersioning.html

----

How can I use Core Data with the *bundle* file format? I'm familiar with doing it without Core Data.

----

Somewhere - I believe on this site - I read something about being able to drag a model (or something) into IB to have it build a prototype interface. Am I losing my mind or is that true? If so, how do I do it?

*Switch to Xcode and then option-drag an entity from your data model in Xcode to a window in Interface Builder.  It will give you some options for generating an interface for the entity, and when you choose one of them it will generate a basic interface that's all wired up using bindings.*

oooh that's pretty sweet. i gotta try that

See: http://developer.apple.com/documentation/DeveloperTools/Conceptual/XcodeUserGuide21/Contents/Resources/en.lproj/02_04_design_data_model/chapter_15_section_5.html

----

How can I initialize objects newly added to the object graph by a controller? I had thought I could override the add<key>Object: method of the owner, as that is what the controller is hooked up to, but that never gets called, nor does insertObject:in<key>AtIndex:. I tried overriding the onAwakeFromInsert method of the NSManagedObject sub-class of the newly-added object, but that gets called before the relationships are put into place, and I need those. Any ideas?

*There's an example here: *
http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/03_CustomClass/chapter_4_section_3.html

Yes, that example uses onAwakeFromInsert. I tried that; that gets called before the relationships are put into place, and I need those relationships. I'm looking for something analogous to awakeFromNib, where everything is ready to go, or else for a different technique.

*The methods you're referring to are mutable array KVC methods.  Core Data represents relationships as sets, so if you want to hook into the relationship manipulation you need to override the mutable set KVC methods.  Also, be sure to override them properly - see the documentation or the code that the modeling tool will generate for a to-many relationship accessor for an example.

I'm not sure though what you're really trying to accomplish.  Can you explain it in terms of your goal rather than the mechanics of getting there?*

**
How about overriding init: in your NSManagedObject subclass?

Another possibility that I have used (though it's probably not sound design) is writing a custom addMyObject: method in my App Controller and hooking the add button to that. I can set all the properties and relationships to make it a member of whichever collections I need. This would seem to solve this problem, but it obviously is not going to work if add: is called from anywhere but my custom button action.
**

I'll describe what I'm trying to do. I have a managed object (Form) with a relationship to a collection of subsidiary objects (each a Field). These subsidiary objects have to be ordered; I'm using an "order" property. When I add a new Field, I want to place it at the end of the collection. I intend to do this by getting the current count of objects, and setting the new object's "order" to that.

I did grab the accessor for the collection. "addFieldsObject:". Unfortunately, it is not called. I set up a breakpoint that was not hit. The add is done by a button hooked up to the add: action of the NSArrayController handling the collection. The "contentSet" binding of the NSArrayController is hooked up to the "selection.fields" binding of the Form 's controller, and "fields" is the KVC/CoreData key for the collection. The array controller's entity is set to "Field".

AFAICT, everything looks like it ought to work.

**
IIUC, you could try subclassing NSArrayController for your Fields object and ovverride its add: method to populate the order variable with a count of the controller's arrangedObjects.
**

**
No, simply implement BOTH add<key>Object: and remove<key>Object: (-Dan-)
**

----

What does the "Transient" option mean in XCode 2.0's data modeler? I assume this is covered somewhere in the Core Data documentation, but searching for 'transient' yields confusing results for someone clueless about the concept and doesn't know any other keywords to use. :-) Help, please? I find some of the documentation for Tiger a bit ... frustrating.

*According to what I've read, it means the attribute will never be actually stored in the back end. So it can be used for temporary values.*

So can I use this for a calculated value like a dynamic filepath?

*Yes, and you can use it for values that CoreData can't represent. See http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdNSAttributes.html. *

But to use a transient attribute as a calculated value, I'd have to create a subclass of NSManagedObject and implement this, setting the entity's class to MyManagedObjectSubclass ... or whatever, right? so -calculatedFoo would require MyManagedObjectSubclass to contain its own -foo / -setFoo KVC-compliant methods right?

*Correct.*

----

From: http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/04_Department/chapter_5_section_2.html

*"Because of the way Core Data operates, it is not possible to easily support autosaving in an NSPersistentDocument-based application. Core Data cannot save to a store and maintain the same changed state in a managed object context, all while keeping an unsaved stack around as the current document. For similar reasons, NSPersistentDocument does not support Save To operations."*

What does this mean? My application implements an auto-save scheme for individual external files managed centrally through a main project file (the NSDocument subclass) in a project setting. Creating a new external file automatically adds it to the project, so if the user adds a new external file then doesn't save the NSDocument subclass, it puts the whole project in an inconsistent state. So what I'm asking is *why* can't I auto-save? Is there *any way* around that without dropping Core Data? What a miserable limitation in my case! :-(  Reading this more carefully, however, suggests only that it can't make on-the-fly saves, correct? So forcing the context to -save whenever I tell it to should work, right?


----

I could be wrong, but doesn't a "persistant" object imply that it is continuously autosaving? 

*No, persistent (in this case) means that it's something that *gets* saved / stored - at some point, not necessarily automatic. It's non-volatile user data. In a text editor, for instance, the document's text is persistent (gets saved) while statistics like word count do not (should not, anyway, as it should usually be calculated on the fly).*

----

The way you specify search predicates for a NSSearchField is quite straight forward. You can display only the employees whose manager is X.
But how do you display the people managed by X by selecting its name in a pop-up menu? How do you set the tableView�s �value� binding?
(How) do you have to set the binding of the �filterPredicate� NSArrayController (which is very poorly documented)?

*I found a non-binding solution, the pop-up sends an action to a controller which performs a fetch on the NSArrayController:*
    
-(IBAction)fetchEmployeesForManager:(id)sender {
NSString * manager = [sender titleOfSelectedItem];
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"manager.name like %@", manager];
[employeesAC setFetchPredicate:predicate];
/*You actually don�t need to perform the [employeesAC fetch:nil];
as settting a new predicate does so.*/
}


----

I'm trying to get unit testing (using UnitKit) working with my Core Data Model and Classes. I'm having trouble and am looking for an assistance. -- MikeZornek

----

Regarding the NSManagedObjectContext     -reset method - if I perform an operation that faults in a large number of objects, would I then save the state and 'reset' the context to release/destroy all those objects to save on resource usage? Is that the purpose of     -reset? -- Update: Calling -reset killed all my unsaved changes to the object graph. I still don't know if it's just unsaved changes since last save, or if it completely resets the context to a 'new document' state regardless of the persistent store.

*Using     -reset is effectively similar to deallocing the context and creating a new one. It "clean slates" the context, clearing all of the pending changes out. If you don't     -save, any changes that took place since the last save will be gone. When a PersistentStore is added or removed from the PersistentStoreCoordinator, you must send     -reset to the ManagedObjectContext so that it doesn't get confused. I believe this is the most common use of     -reset.* -- ScottStevenson

----

Here's a method to do shell-like globbing from a NSSearchField. I'm sure it can be much improved, and hope it will be ;) This is from my first CoreData project.

*That's great! One question ... what's 'shell-like globbing'? ;-)*

well, it's doing globbing similarly to how the shell does it ;) ie 'car** matches 'car' 'carton' and 'cartoon', 'ca?' matches 'cat' and 'car' and so on.

*So would this be specifically useful in, say, searching many entities with large quantities of text? I mean, would this be better than iterating through an array of objects and doing a rangeOfString type search?*

'Better' depends on what you're looking for, but this is certainly easier. That's the entire code needed to handle displaying the search matches in a table view, handling insertion while the view is narrowed, removal and all the other expected stuff. Not to mention you can get regex matching, and search on multiple criteria just by changing the predicate.

    
-(IBAction)search:(id)sender
{
	NSManagedObjectContext * context  = [[NSApp delegate] managedObjectContext];
	NSManagedObjectModel   * model    = [[NSApp delegate] managedObjectModel];
	NSDictionary           * entities = [model entitiesByName];
	NSEntityDescription    * entity   = [entities valueForKey:@"Post"];
	
	NSString *searchString = [sender stringValue];
	
	if ([searchString isEqualToString:@""])
	{
		searchString = @"*";
	}
	
	NSPredicate * predicate = [NSPredicate predicateWithFormat:@"text LIKE[c] %@" argumentArray:[NSArray arrayWithObject:searchString]];
	
	NSSortDescriptor * sort = [[NSSortDescriptor alloc] initWithKey:@"text" ascending:YES];
	NSArray * sortDescriptors = [NSArray arrayWithObject: sort];
	
	NSFetchRequest * fetch = [[NSFetchRequest alloc] init];
	[fetch setEntity: entity];
	[fetch setPredicate: predicate];
	[fetch setSortDescriptors: sortDescriptors];
	
	NSArray * results = [context executeFetchRequest:fetch error:nil];
	NSLog(@"%@",results);
	
        // arrayController is an outlet in IB. I'm sure there's a better way to get ahold of this.
	[arrayController setFetchPredicate:predicate];
	[arrayController fetch:self];
	
	[sort release];
	[fetch release];
}


----

Apple's NSPersistentDocument tutorial thing shows how to use -    awakeFromInsert to automatically (and crudely) increase the id of each record added by one; therefore the first would be 1, the second would be 2, and so on. Unfortunately, and here is the crude part, it does this by means of a static int, and so it seems to me that if you relaunch the application it's going to add the next one at 1 again.

So what I'm wondering is, how do you, in your NSManagedObject subclass, find the max id--or any property, really--for instances of an entity? Does one use a predicate involving     array[last]? I'm well beyond lost here, so any help is very much appreciated.

...well, a little more time and I've figured it out. Mostly.

    
-(void)awakeFromInsert {
	[super awakeFromInsert];
	
	[self setValue:[[NSDate date] descriptionWithCalendarFormat: @"%b %e, %Y" timeZone: nil locale: nil] forKey: @"dateIssued"];
	
	id request = [[[NSFetchRequest alloc] init] autorelease];
	
	[request setEntity: [NSEntityDescription entityForName: @"Invoice" inManagedObjectContext: [self managedObjectContext]]];
	[request setPredicate: [NSPredicate predicateWithFormat: @"id > 0"]];
	[request setSortDescriptors: [NSArray arrayWithObject: [[[NSSortDescriptor alloc] initWithKey: @"id" ascending: YES] autorelease]]];
	
	id error = nil;
	id array = self managedObjectContext] executeFetchRequest: request error: &error];
	
	if(array) [self setValue: [[[NSNumber numberWithInt: [array lastObject] valueForKey: @"id"] intValue] + 1] forKey: @"id"];
}


Don't get me wrong, this works beautifully. Unfortunately, when you click "Add" in my interface, it adds not one but two rows... they both reference the same object, however, so changing one changes the other, as does removing one. Maddening.

After a little experimentation it turns out that this is happening when the fetch request is executed-- no big surprise there, I know-- so I'm wondering if something's the matter.

Also note that I do nothing with the error value.

*As an aside, does anyone know how to set data cells for table columns in IB? I can set it back to default with the big ol' button, but haven't been able to figure out how to set it in the first place... I was hoping it'd be like adding a formatter to the cell, but no joy.* **Oh man I'm dumb! It's right there in the tableview palette. Oi!**

**I just use [[[NSNumber numberWithDouble:[[NSDate date] timeIntervalSinceReferenceDate]] as the ID. There's probably a better way, but unless you're creating many objects at full speed, you're pretty much guaranteed a unique ID. Even at high speed, it's not very likely to be executed fast enough to get the same ID. For my app, the user is clicking a button (or whatever) to create objects, so this works just fine.**

*That's a good idea, but for invoices I'd prefer the IDs to be immediately sequential, rather than just ordered. If I can figure out why the double references in the table are going on, I'll post it here, to be sure.*

**In that case, why not just store the latest number with the persistent data, restoring it when the document is opened so you can pick up where you left off? **

*This sounds like it'll work nicely, and ought to avoid the fetch request and therefore the weird double entries. Thanks!*

-- RobRix

----

With the same project, I have another question. I've got a simple calculated value set up which operates on a relationship. That is, each of the items in the relationship has a price and a quantity, and the calculation simply multiplies the one by the other. This is working beautifully, however, the only thing I can get it to properly update on (by means of KVO) is the items relationship, which doesn't actually reflect any changes on its constituents, only to the set itself. Therefore you have to add and remove a "dummy" item in order for the invoice to reflect the new amount.

I've tried setting my Invoice class as an observer on items.quantity and items.price but neither of those is working, unfortunately. Anyone have any ideas?

Oh, and also, I've found out that in a table view, if there is a column whose data cell is a pop up menu, as might be the case if you had IB generate the interface for something with inverse relationships to an Entity-- like an Invoice's connection back to its Project-- and you have a row selected, and then click on that menu on another row... the array controller's selection is not updated at the time that the menu is created. Thus if you are populating that menu based on the array controller's selection, you will tend to see the -    description of an NSManagedObject (or your custom class) showing the fault for the value you have selected (if any) and otherwise the values for the selected row... not for the row you're editing. Ouch.

-- RobRix

For the first question,
you could add the parent entity 'Invoice' as an observer by overriding the setters for the items (back to glue code...),
or if you only need the update in the GUI, you create a method in the parent called e.g. 'updateKVO', triggered by the change in the price and quantity text fields (the usual target/action mechanism), and in this 'updateKVO', you do
     [self willUpdateValueForKey:@"items'];[self didUpdateValueForKey:@"items"]; --CharlesParnot


----

How do you use the filterPredicate in an NSArrayController and its bindings?? And what�s the point?

It is simply use to filter the array, and select only those elements that you want to keep in the GUI. For instance, if the objects in the array have a 'isDeprecated' key, you could use a predicate     isDeprecated==0 in the NSArrayController to not display the deprecated objects.For this, you type the predicate in the field, and then click 'Set Predicate' and then the predicate is parsed and formatted and ready to use. The predicate is not used in the bindings per se, but only to restrict the number of elements that the GUI will display.

Thank you for answering. This seems interesting but only if you already know the possible values and the value you want to filter the array with. How do you do this: filter an employees array by manager name as selected in a pop-up menu?

*Forgive the "RTFM" response, but: http://developer.apple.com/documentation/Cocoa/Conceptual/Predicates/index.html#//apple_ref/doc/uid/TP40001789  ...this will answer your questions if you actually take time to read it and not skim it. Your assumption that you can only use NSPredicate (and fetch requests) when the value is known is incorrect. You can use substitution variables (which is how the search field works). Honestly, "TFM" is exactly what you need to answer these questions.*

Thank you very much but I�ve read it and didn�t find it useful for the average user. It lacks examples of how to use the elements of the API **together**. For example, about the fetched properties, you can only read this:

**Fetched Properties**
*Fetched properties represent weak one-way relationships. A common example of a fetched property is an iTunes playlist. Songs don't know what playlists they belong to. Moreover, songs in a playlist might belong to another user, and so appear only when that user is connected. In Core Data, a fetched property is specified by a fetch request for the destination entity. In the employees and departments domain, a fetched property of a department might be "recent hires" (employees do not have an inverse to the recent hires relationship). The destination entity is Employee, but the relationship itself is dynamic and must be recalculated periodically to ensure it is up-to-date�you do this by refaulting the source object.*

----

Hello! I�m saving two dates in my data. When I reopen the app a calculated value is supposed to give the number of days between the two dates using the years:months:days:hours:minutes:seconds:sinceDate: method. I get the error [NSCFDate years:months:days:hours:minutes:seconds:sinceDate:]: selector not recognized [self = 0x33f840] and I have to manually reenter each date twice until the calculation will take place normally. What is happening? Do I need to convert NSCFDate to something else??

*
The method you're trying to call is a method of NSCalendarDate, and your error message indicates that the object is an instance of NSDate (actually of NSCFDate which is a private concrete subclass of NSDate).  Either convert your date object to a calendar date or do something else.
*

Thank you!

----

Hi! How do you enforce a value to be recalculated and updated in the UI? In my firstObject, I have a transient attribute that calculates a sum up of different attributes found in several secondObjects in a to-many relationship. When one of these secondObjects changes, it sends a notification and when the  firstObject receives it, I want it to make the sum up textField update. How can this be done? The problem is that you can�t say to firstObject:
    
NSArray * keys = [NSArray arrayWithObjects:@"mySecondObjects.AttributeToSumUP", nil];
[self setKeys:keys triggerChangeNotificationsForDependentKey:@"mySumUpAttribute"];
 --FloFl

*Check out     + (void)setKeys:(NSArray *)keys triggerChangeNotificationsForDependentKey:(NSString *)dependentKey.*

Oh, thank you! Did you read the code above?? A key doesn�t seem to be able to be dependent of keys from a relationship... --FloFl

I figured out this one, and I'm wondering if there could be a better way to do it, but this seems to work.  I roll my own implementation of setValue:forKey:

    
-(void)setValue:(id)value forKey:(NSString *)aKey
{	
	MQUsage *u = [self valueForKey:@"usage"];
	BOOL notifyUsageOfTimeChange = (u && [aKey isEqual:@"timeDuration"]);
	/* if somebody calls setValue:ForKey: to change "timeDuration", 
		the MQUsage object, which has keys for the sum of timeDuration,
		will be notified with willChange/didChange below.*/	
	if (notifyUsageOfTimeChange)
		self valueForKey:@"usage"] willChangeValueForKey:@"totalTime"];			

	[super setValue:value forKey:aKey];

	if (notifyUsageOfTimeChange)
		[[self valueForKey:@"usage"] didChangeValueForKey:@"totalTime"];	
}


A total kludge, and there chould be a class method for propagating changes down dependent *key paths*, not just keys, but this does work.  When you start having many many keys changing under different situations, it might get too nasty to deal with.

----

I'm develping a school gradebook app, and I'm having trouble getting Core Data to work. I have two entities, Student and Activity, and array controllers in IB. Both entities have attributes and relationships to each other. In IB I have a table with two columns: student and grade. The table's connected to the student array controller. Another table holds a list of activities, connected to the activities array controller. What I want to do is have the grade column show the respective students' grade for the selected activity, and change when selection of activity changes. I've tried everything I can think of. Is there an easy way to do this? --[[LoganCollins

*
I�m having exactly the same problem for exactly the same type of application! Any help is greatly appreciated! --FloFl
*

** This one comes up very frequently. It is really a Bindings question, not Core Data. One good example is Scott Stevensons Bindings article at http://www.cocoadevcentral.com/articles/000080.php which illustrates how to acheive a Source/Detail linkage. In summary, you need to bind the contentSet binding of your Activities array controller to the selection controller key of your Students array controller. - AlexClarke **

That doesn't do exactly what I want. I've tried it before. That produces a different set of activities for each student. I want to be able to have a list of students and a list of activities in the document that I can have share a common grade. Each student will have one grade for each activity, each activity will have an array or set of grades for the students. In the students table will be a column for the student, and a column for each activity display the grade for each student, and finally an overall grade column that averages the others with bindings, which I already have implemented. Is there any way to do that? --LoganCollins

*
--Same for me!-- Yes, thank you Alex, that�s a very good tutorial. But my UI goal is a little different: instead of displaying the grades for one activity at a time, I�d like to have the students in the first column then their grades in the other columns, one column for each activity. How can this be done? --FloFl
*

I believe that you would want to create third class of object. Call this object a "report". It has two relationships (pupil and subject) and an attribute (a grade). Pupil would be one of the students, and subject would be one of the activities. grade which would be quantitative performance measurement, such as a letter grade (A, A-, B+) or equivalent. 

An inverse relationship for both pupil and subject would also be created: Each student would have a one-to-many relationship to their reports, while each activity would also have one-to-many relationship to its reports. The report relationship serves as a cross-reference. It can be used to fill a column or populate a row either by student or by activity. Furthermore, the collection of reports leads to more flexible queries, such as: "find all students who received an A in any subject". 

There will need to be some custom code that runs every time a student is added to ensure that the proper set of reports is also created. This task could probably be automated to some degree as well. I would have to think on it a bit more before I could suggest how.

----

Hello,

I have an NSManagedObjectContext bound to a table via an NSArrayController. When I programmatically insert my managed objects into the context, the tableview does not reflect the changes. However when I wire up a button to the arraycontroller add: action, when the button is clicked, the result always shows up.

How can I make the the table-binding reflect changes in the managedObjectContext when I programmatically modify it?

Does checking 'Automatically prepare contents' help?

See http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html#//apple_ref/doc/uid/TP40001802-DontLinkElementID_14 ...

----

NSArrayControllers bound to CoreData entities don't seem to allow multiple selections in table views.  Is there anything I can do so that multiple objects can be selected?

I use many table views bound to array controllers using Entities, and multiple selection is allowed when I check the box in the TableView attributes.

----

How do I create a SearchPredicate binding for an NSSearchField when I want to search within objects linked by a to-many relationship? If I want to return only objects for whom the object linked by a to-one relationship called *fubar* has the searched *name* property, I can just do make the predicate:

fubar.name contains[c] $value

But if *fubar* is a to-many relationship (*fubars*), and I want a match for any Fubar's *name*, I thought this predicate should work:

fubars.name contains[c] $value

But this doesn't work! How should I do this?

Use ANY...
See http://developer.apple.com/documentation/Cocoa/Conceptual/Predicates/Articles/pSyntax.html#//apple_ref/doc/uid/TP40001795-215891

----

How can I get an array of unique values from a particular property.  For instance, say I have a "first names" property.  How could I get every unique instance of a first name?  Do I have to iterate through the data myself or is there a handy way to do this easily?

----
use @distinctUnionOfObjects: 
    
NSArray* uniquedFirstNames = [thisIsAnArrayOfPersons valueForKeyPath:@"@distinctUnionOfObjects.firstName"]; 

thisIsAnArrayOfPersons, is the result of a fetch for example.
----

----

I'm toying with downloading core data databases online and swapping them out with the currently used database.  How can I update the managed object context and everything else to note that the file has been replaced and update all the UI elements accordingly?

*Close it and re-open it.*

----

How do you make the file in a NSDocument CoreData app as a folder (with extension; see FolderBasedDocuments), and save other files, whatever they are, in that package with the document's saved file, when CoreData saves the document data in that XML or whatever stored file inside of said folder?

----
You referenced the "FolderBasedDocuments" page but you don't seem to have read it. Read the very last section on that page - you **cannot do this with Core Data**.

----

The 32k limit was hit and the last question and a reply has been moved to CoreDataQuestionsPageTwo.

