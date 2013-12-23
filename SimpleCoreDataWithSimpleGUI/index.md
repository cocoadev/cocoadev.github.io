---
layout: page
title: SimpleCoreDataWithSimpleGUI
---



Hello CocoaDev!

I try to make perhaps very simple app but I got lost somehow.
It is a simple database designed (with Data Modeler) and accessed through Core Data, where every Entity has 3 Attributes: "A", "B" and "Result". The wonderful Cocoa Dev Central article on how to Build a Core Data App was very helpful.

Then I add the user interface with Interface Builder - NSArrayController for all the Entities bind to SomeAppDelegate.m and a NSTableView with "Add" and "Remove" NSButton and 3 NSTextFields corresponding to the Attributes. And one another NSButton "Solve". (Btw. those NSTextFields will contain numbers.)

Now, everything works perfectly! I can add, remove and edit items and the stay in the Data Store as well.

But I would like, if when I click the "Solve" it e.g. adds a value of "A" and "B" and persistently stores it in "Result". Where and how should I do that? I tried many things but apparently I was not successful. 
Please, I am very new to Cocoa, is there a (simple) solution for this? And what if user filled in not a number in either "A" or "B"?

I am very thankful for any help!

- Ecir Hana

PS: And there is also another thing I would like to ask. Imagine, the above-mentioned database has another Entity "Preset" with attributes "Name", "X" and "Y". Another panel serves as the GUI. On the main window there is another NSPopUpButton in PullDown mode called "C" with Binding "content" bound to "Preset"'s arrangedObjects with Model Key Path "Name". Now, please, how could I make that if when I select an item of "C" it fills "A" and "B" with "X" and "Y" of selected preset accordingly.
I really appreciate any help and thanks in advance!

----

Hi Ecir

Here is my solution on how to get the current selected row from an entity, and then alter one of its properties. You might want to set your tableview not do multiple selection, or you have to make this function to cope with several NSManagedObject.

    
- (IBAction) calculate:(id)sender {
if ([recordsArrayController selectionIndex]!=NSNotFound) { // *1
	NSManagedObject * myRecord  = nil;
	myRecord = [recordsArrayController selection]; //*2
	[myRecord setValue: @"nemo@pixar.com" forKey: @"notes"]; //*3
}
}


*1: recordsArrayController is an IBOutlet created in you controllerclass (Mine is a subclass of NSPersistentDocument)
*2: [NSArrayController selectionIndex] will only give you the selected index, but NSManagedObjectContext store its records in a NSSet. Either could I find a way of sending the NSManagedObjects objectID and then use it to retrive the right NSManagedObject from NSManagedObjectContext. The solution howevery way simple, because the superclass of NSArrayController which is NSObjectController had a suitable functions. 
*3 Now that you got the right NSManagedObject, is just childsplay to do the edit stuff.

Hope this is what your was looking for!

Gerherd Sletten (gersh.no)

----

It's generally bad form to approach others who've worked hard to learn a skill with verbiage that insists on a simple solution, whether you're new or not. The solution is the solution, easy or not.

In any case, when your 'solve' button is pressed, you can ask your array controller for the current selection, then call your -solve function (whatever and wherever that is), storing the result in the entity instance's appropriate key ("result"). Please note, you specify capital letters as the attribute names. This is not KVC (key-value coding) compliant. Re-read the tutorial; this information is covered in detail.

I suspect the real problem is that you want 'result' to always be available. In this  case, you'd probably be better off making the "result" attribute transient (not stored) since you can always get the result from the "a" and "b" attributes. Search for and read up on "dependent keys" and "transient attributes". You'll want to create a custom subclass of your entity (a NSManagedObject subclass) and create a     - (NSNumber *)result method that calculates the result of "a" and "b" and returns the value on the fly. Using dependent keys, any time "a" or "b" is changed, the -result method can be called and any part of your interface that uses the result value can be updated automatically. 

In other words, you still have some reading / learning to do. Apart from that, the only other option is to get someone to do it for you. This isn't a simple subject, so you're going to have to work for it.

----

On the contrary, I think the original question was in very good form because the poster clearly doesn't want to waste other people's time. Anyone responding is spared the chore of describing a detailed, complicated solution, because the poster has told us up front that he is interested only in simple ones. And he probably has little interest in being lectured either. 

*If you're looking for solutions to be handed to you, a lecture is in order. If on the other hand you're asking where to find the answers and how to approach a problem, specifying that you're new and want it the easy way is bad form (the word "whining" comes to mind).*

----
I disagree with the entire notion of lecturing anyone regarding technical Q&A on this site. Presuming that a lecture is in order is presuming way too much information regarding the motivations of the original poster. They may be new, under a deadline, and completely dis-interested in the topic. They may have family or emotional pressures, or a thousand and one other things that make a lecture completely inappropriate. People accept and respond to lectures from individuals they have come to respect. In turn, this respect comes from interaction and trust. Effective lectures come from individuals who have taken the time to consider all the circumstances regarding the question and the life of the questioner. A random lecture from a random individual on a wiki is nothing more than wind and noise.
--TimHart - *Appreciating the irony that this post can in itself be considered a lecture.*

**And a rather long-winded and noisy one at that. :-)**

----

I would like to have "Result" stored because when if there would not be any "front-end" to the database one will still be able to retrieve the results.

"
In any case, when your 'solve' button is pressed, you can ask your array controller for the current selection, then call your -solve function (whatever and wherever that is), storing the result in the entity instance's appropriate key ("result"). Please note, you specify capital letters as the attribute names. This is not KVC (key-value coding) compliant. Re-read the tutorial; this information is covered in detail.
"

Honestly, I do not understand this. I re-read this paragraph many times but I just don't get it.

On the screen, there is the first NSTextField which is bound to Entity attribute "A", i. e., in Inspector > Bindings > value > Results(NSArrayController), selection, a. Similar applies to the second field "B". 

My -solve function would add [selectedEntity valueForKey:"a"] and [selectedEntity valueForKey:"b"], right?

Now, what on earth is "selectedEntity"? How is it possible to get it? Should it be passed to -solve through its argument? Or, is there another way, how to access it? And what should the -solve return? Nothing, because it sets up [selectedEntity setValue:myResult forKey:@"result"], i. e., the third NSTextField? Or, should it return the result of A adds B, so I can (somehow?) bind it to my GUI via Inspector? Maybe, to bind it to NSButton "Solve"? With another words, how to invoke -solve when "Solve" NSButton is pressed? Or am I completely wrong here?

As you can see I am quite lost here. I really try to read and to understand many articles, tutorials, guides, etc. and I also re-read the above mentioned article but I am just lost.Please, would you be so kind, and reveal more?

And, the title "simple" I used was based on the fact than I would like to add two numbers and store the result. And that thanks to that article it was easy and simple for me to create and manage the whole database, add a GUI a get undo "for free".

Thank you very much.

- Ecir Hana

----

See:

- CocoaBindings

- NSArrayController, NSObjectController

- KeyValueCoding

----

You are going about this all wrong. Regardless of how the data is being viewed, any time your object is asked for -result it will calculate the information on the fly. There is **no need** to store this value. None at all.

----

Maybe the reason for it would be that it is little bit more safe to store the result in terms that you wouldn't need exactly this app to access the result data.

*That's a hard sell for this particular situation. If the result is a calculated value from two stored values, any program reading the file *should* be interested in the variables and not the solution.*

----
What�s wrong with asking for some help with a difficult matter, haven�t we all been there? The best way to approach these matters (and that�s what Ecir seems to be trying to do) is to start with a simple app. Once it is clear how everything works, he can extend it. I would say that it is also bad form to patronize him because of his word use because clearly his native language isn�t English and has therefore seems to have some problems with reading the tutorial and documentation.

----

It seems to me the single most important thing is to know how to set and get the values of selectedEntity Attributes.
(I guess set / get is done by selectedEntity valueForKey:someAttribute / selectedEntity setValue:someValue forKey:someAttribute, so the question is how to access this selectedEntity.)

The -solve function can be triggered via Action Methods, that means to have the function in myController and bound to Button Action's in IB. I also tried to bound the myController to NSControllerArray via Outlet, hoping to get the current selected Entity - through Outlet selectedObjects and then [selectedObjects objectAtIndex:0] valueForKey:someAttribute. Sadly, this does not work and even "binding selectionIndexes in the NSTableViews to selectionIndexes in the controllers" (as I saw elsewere on this site) did not  fix this.

Apparently, I am doing something wrong but I have no idea what. I am thankful for any help.

----

Hi, I got the same problem. An example would make my day.

gersh

