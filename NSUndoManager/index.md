---
layout: page
title: NSUndoManager
---

Handles the undo stack. Generally used in your document (model) class, but may be used in the controller class for handling things like the selection or the names of the undo items.

----

One note: you have to setActionName on the undo manager of your document, not of your controller. Use something like:

    self document] undoManager] setActionName: @"action name"];

When setting the action name from the controller class.

And better yet, use [[NSLocalizedString to get the string name, allowing for easy localization:

    self document] undoManager] setActionName:
        [[NSLocalizedString(@"Some Action",
                          @"Action name for some action.")]

And then use genstring command line tool to generate the localized string file.

----

See http://developer.apple.com/documentation/Cocoa/Conceptual/UndoArchitecture/
----

This page (currently in draft form) contains information about implementing undo in a Cocoa app:

http://www.ex-cinder.com/downloads/undo-article.rtf

-- KritTer (not the author of the page in question)

I'm on the road currently (without OS X; no .rtf file access at this time), is there any chance somebody could talk to the author and see about reprinting here? -- RobRix

Why would we want to reproduce it here? Do you feel that having to download a .rtf file is too inconvenient? -- KritTer

It can show up in the cocoadev site-wide search. ++MarkDalrymple

Where I was at the time (I believe that was back in April, when I was visiting Georgia) it would have made a difference. Still, convenience is a reason for much in terms of software development, so it might still be worth considering rolling at least some of it into this site. -- RobRix

----
Done, see NSUndoManagerTutorial

----
Has anyone found out how to use NSUndoManager in non-Document-based applications? It's possible to create a NSUndoManager object and send it     -prepareWithInvocationTarget:  but Undo and Redo are never enabled in the Edit menu. -- Merman

----

To add undo support to a non-document  based application, you can add this method to your window's delegate:

    
- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)sender
{
	return undoManager; // We created this undo manager manually
}


-- RyanBates

----

To expand on the above a bit, I usually create a category on NSUndoManager to return a singleton, shared undo manager object. I can then return this object from windowWillReturnUndoManager in all my windows, so the Undo / Redo menu items work, no matter which window happens to be key.

----

Another interesting article about undo: http://www.macedition.com/display_story.php?articleid=1007

----

With regards to adding the windowWillReturnUndoManager: method above, and creating a category on NSUndoManager, all that may not be necessary. The docs state: "If this method is not implemented, the NSWindow object creates an NSUndoManager object for the window." So if you have a single window, non document based application, passing a undoManager message to your window in your insert and remove methods could be the li'l dab that'll do ya.

----
I'm wondering why the following code does not work?
    
	self undoManager] registerUndoWithTarget:[someObject mutableArrayValueForKey:@"array"]
							   selector:@selector(removeObject:) 
								 object:myObject];

Instead, I had to workaround this way:
    
	[[self undoManager] registerUndoWithTarget:self
							   selector:@selector(removeGivenObject:) 
								 object:myObject];

-(void)removeGivenObject:(id)myObject {
	[[someObject mutableArrayValueForKey:@"array"] removeObject:myObject];
}

I guess it has something to do with the mutableArrayValueForKey-function and the time when it's actually called?

----
I'm not sure why it doesn't work, but maybe if you can be more specific than "does not work", somebody could answer you.

*[[NSUndoManager doesn't retain its target...probably the mutable array is being deallocated before it's actually used. --JediKnil*

Thanks for the explaination.

----

I am wondering how to stop the NSUndoManager of an NSDocument from firing an updateChangeCount call?  I am using the undo manager to undo and redo changes that when made are automatically saved to disk so the updateChangeCount call is unnecessary.  I am calling the undoManager through prepareWithInvocationTarget.  Thanks for any help!

