---
layout: page
title: NSDocumentProblems
---

I am unclear as to when     makeWindowControllers is useful.  In the nib file that goes with Document.m, any number of windows 
can be opened from the MainMenu by default. Is it the point to use 'makeWindowControllers:'
 if one wishes to have more than one kind of window in this nib and/or more control of the windows?  
Other nibs can have their own window controllers.  Or is the 'makeWindowControllers:' command to be used for 
all windows in all nibs?

-Paul

----

First of all,     makeWindowControllers: is only intended for the document's windows (not for windows in MainMenu.nib). Basically, this method allows you to use your own NSWindowController subclass to manage each of the document's windows. With simple document based applications you do not need to worry about subclassing NSWindowController, but if the window becomes complicated, or if there are multiple windows per document, you probably do not want the MyDocument class to manage all of it's windows and contents single handedly (it becomes messy). Here you can make an NSWindowController subclass for each of the document's windows and tell the document to use those window controllers instead of itself - you do this through the     makeWindowControllers: method. It might help to read this: http://developer.apple.com/releasenotes/Cocoa/NSDocumentFAQ.html#Should%20I%20subclass%20NSWindowController

Hopefully I didn't confuse you more. :) -- RyanBates

----

Ryan, many thanks---again!  Your comments plus the FAQ were very useful.  I also found an example on the web by Mike Beam. I think I've got it.

-Paul
----

I'm having issues with NSDocument, this is my first time using it so I'm probably messing something up.
I put some breakpoints in to see what happens to objects & such when it starts up. It starts up and a new empty document is created, and looks fine.

I go to open a recent file, so far nothing really happens besides debug stuff that prints out what file it's supposed to be opening.

Weird thing, but this could be my inexperience, all of the IBOutlets in my NSDocument sub-class or whatever (called it DBView) are now 0x0 in the debugger...
what happened? Did I do something wrong and I'm totally doing NSDocument stuff wrong?
 any help would be appreciated, thanks :)
 ---JeremyK
 ----
 *Did you connect those IBOutlets to something in InterfaceBuilder? Did you re-check those connections?*
----
At what point in the creation of your document are all the outlets nil?
----
They're all connected, re-connected too before checking the debugger
the point I checked in the debugger was the - (BOOL)readFromFile:(NSString *)fileName ofType:(NSString *)docType method
there was also a breakpoint before in the - (void)windowControllerDidLoadNib:(NSWindowController *) aController
so that finished, and should then have connections right?

Noticed that the file loading function is called before the nib is done loading... so I'll re-work this and get back on any issues or if it works, I'll set a variable with the file it has to open and have it udpate the interface when it's done loading nib and that might work!
--JeremyK
YAY! it works, I just wasn't paying attention to the order of what was being called I guess, silly me, thanks all, now back to figuring out FMSqlite someone made to use so I don't have to require /usr/local/lib/libsqlite.a or whatever --JeremyK

*I shoulda mentioned the order. Yeah, it'll bite you if you're not careful. But congrats on its current workingness!*

----

This seems like a good time to plug NSWindowController. While you can use NSDocument as its own window controller for simple situations, I strongly recommend using NSWindowControllers instead. NSWindowController gives you a better understanding how/when your nib file gets loaded, properly separates your UI handling (read: View) code from your document (read: Model) code, and is scalable.

-- MikeTrent

----

OK. I'll take off from there...

My document-based app creates its own window controller using:

    - ( void ) makeWindowControllers

in the NSDocument subclass. Now most of the work is done in the window controller subclass.

What I am curious about is how the document controller initializes the relevant ivars that properly belong to the document (cf "Model") subclass.

Maybe I've got that backwards, though. I could look at it as the document subclass copying over the controller ivars before archiving them...Hmmm?

So far I have found that allocating and initializing instances of these in the window-controller-subclass     init method fails, but if I use its     awakeFromNib
method to do this, then I have valid pointers to them in the controller. Is this the way it's s'posed ta be?

Does this result from what Jeremy was referring to above as: *the file loading function is called before the nib is done loading*? Or is it something else?


Yes, no, that's it:

*
The NSDocument is unarchived, and its ivars are initialized
*
The runtime calls the     - ( void ) makeWindowControllers method on the document. The NSWindowControllers get created.


So yes, you got it backwards, I think. The NSDocument is the model and is loaded first. NSDocument probably saves a few additional ivars (in addition to yours), but it probably does not save anything of the NSWindowControllers. It is only at runtime that NSDocument decides which NSWindowController to use, and you make some new instances of those every time in the      - ( void ) makeWindowControllers. Things like window position etc... are saved elsewhere, I believe.

----

For getting things started in various ways, my document class has:

     - ( void ) init 

     - ( void ) windowControllerDidLoadNib: ( NSWindowController ) aController 

     - ( BOOL ) loadDataRepresentation: ( NSData * ) newDocData ofType: ( NSString * ) aType 

    dataRepresentationOfType does a

    [ [ self windowControllers ]
			makeObjectsPerformSelector: @selector( grabData ) ];

when saving a file. My window controller has

     - ( void ) awakeFromNib 

     - ( void ) windowDidLoad 

I'm wondering if     awakeFromNib is appropriate or even necessary in the window controller. For awhile, I forgot where to look when some of my
interface controls were not being updated from an archived file - but     windowDidLoad turned out to be the right place to put the code. Yeesh.

     awakeFromNib makes some sense in that the window controller
is the class that is doing most or all updating of the control objects in the view (Documentclass.nib), but didn't work in the above example.

I handle restoring the frame and copying over the necessary document (model ) ivars in     windowDidLoad which calls a utility method to update controls
in the interface. The     awakeFromNib handles things like toolbar initializing

----

With CoreData, how do I know when the document has completed loading? It's not clear from the docs where to put code that manipulates the view according to the model, once the model has been completely loaded. 

What I have is a model object that stores arbitrary data, and I'm using it to store the expanded state of a NSOutlineView. I've got two functions 'saveOutlineViewState' and 'restoreOutlineViewState' which do just that. I've got some notifications 'DocumentDidLoad' and 'DocumentWillSave'. Firstly, why aren't these notifications supplied by default with the NSDocument class - this seems like a severe lack on the classes part. How should we actually tell when those two operations are performed? There are many different functions for loading and saving, not to mention all the IBActions available for performing those same operations.

My main problem at this point is where to hack in DocumentDidLoad. But also, if you have a good idea for the placement of DocumentWillSave, this would be useful to know too. Thanks for your replies.

