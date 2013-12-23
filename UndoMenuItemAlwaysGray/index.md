---
layout: page
title: UndoMenuItemAlwaysGray
---



I'm creating an General/NSDocument based app, and no matter what I do, the Undo menu item always stays gray.  In my code I'm calling registerUndoWithTarget: and I'm sure that's all working because my document knows it's been changed when I try to close out of the app.

Why is the menu item staying gray?

----

By calling registerUndoWithTarget or using invocation based undo, you are only adding that action to the top of the current undo group. You have to finish up by calling General/NSUndoManager's -setActionName:, as this will set the Undo item's title to whatever you choose and will also close the current undo group. For example, let's say I have a method that inserts three files into a document, and for each file that it inserts it registers an undo operation.

    
- (void)myMethod:(id)sender {
     [self insertFile:myFileA];
     [myUndoManager registerUndoWithTarget:self selector:@selector(removeFile:) object:myFileA];

     [self insertFile:myFileB];
     [myUndoManager registerUndoWithTarget:self selector:@selector(removeFile:) object:myFileB];

     [self insertFile:myFileC];
     [myUndoManager registerUndoWithTarget:self selector:@selector(removeFile:) object:myFileC];

     [myUndoManager setActionName:@"Insert Files"];
}


After they are registered using registerUndoWithTarget:, I call setActionName to close the group. So now, when you call this method and then undo it, all three operations are undone instead of just one.

Note that you do not put 'Undo' in the title of the item, as General/NSUndoManager does this for you automatically, and it saves you from having to do a completely different operation for Redo. Hence you get redo for free. Pretty cool, eh?

--General/LoganCollins

----

Thanks for your reply.  Here's an excerpt (in order) from my method:

    
	[storedString appendString:[s map:item]];

	// To undo that appendString there we need to remove last item
	General/filesOwner undoManager] registerUndoWithTarget:self
							selector:@selector(removeLastItem:)
						       object:[s map:item;

	// Other stuff...

	General/filesOwner undoManager] setActionName: @"Add Item"];


I'll change to include localized strings as soon as I know it works in the first place, but my menu item is still staying gray.  And my files still know they're modified.  This is my first attempt at Undo/Redo in Cocoa.  My closest guess is that I may have bungled some default that's normally set on the Undo menu item, but I deleted the edit menu and replaced it with the one off the IB pallette to be sure, and still no dice.  I'm calling these methods from a custom view if that makes any difference.  Also, filesOwner is set to an instance of my [[MyDocument class...  Thanks again for your time.

----

It looks like you're calling setActionName on your controller.  You need to call it on your document.

----

filesOwner is set to the instance of General/MyDocument.  Being new to cocoa I probably confused you with the naming.

    

General/IBOutlet General/NSDocument* filesOwner;



There's a line from my custom view to the File's Owner object in Interface Builder and the connection pane shows a connection to General/MyDocument.

I get "selector not recognized" when I call setActionName "on my document" instead of on the undo manager I got from my document.  I'm probably misunderstanding what you're trying to tell me or accidentally did it right under the wrong name originally.

Thank you again for the reply.

Also, if it's related, I'm not getting the need to save before closing black circle in the close button.
----

I think the setActionName: stuff here is a red herring. If you don't call it, Undo still works - it's just that it ends up with a generic "Undo" in the menu rather than a more explicit message about what it's going to undo. setActionName: doesn't "close the group" as implied here - it just sets the menu's title string. The group is closed automatically at the end of the current event, or you can call beginUndoGrouping/endUndoGrouping to set up your own groups.

Anyway, yes, you call setActionName: on the undoManager you get from your document's undoManager method, not on the document itself.

If the menu remains greyed out, I suspect the problem could be that your view isn't the first responder, or somehow the responder chain is broken between your view and the document. Try digging around in this area. --General/GrahamCox.

----

Interesting.  That makes a lot of sense.  I'll start poking around in that area.  While I'm at it, is there a decent way to simply provide document-wide undo/redo?  I'd like the undo/redo to work on my view (as it IS the document) whether first responder happens to be the view or if they're typing in a text field...?

Update:

My view accepts first responder, processes key events by calling [self interpretKeyEvents:] etc...  How do I go about verifying the responder chain is kosher?

----

You should be able to follow the chain through the _nextResponder ivar of your view in the debugger. In most cases, Undo will work "document wide" as long as you've set things up in the usual manner. In other words, without knowing how the parts of your app fit together, it's hard to advise. Normally, you'll have a NIB file representing your document class and the window + user interface it manages. You'll set the NIB's filesOwner to have a custom class which is your General/NSDocument subclass, and you'll set its window outlet to your window. You'll add views to the window and set the window's firstResponder outlet to your view. You'll also change the plist parameters so that the NIB file is loaded as your default document class. The easiest way to get a handle on this is to just use Xcode to start you off by creating an General/NSDocument-based app for you. By default the custom subclass is called 'General/MyDocument' which I find a bit annoying, but once you understand how it all works it's easy enough to change. So, assuming that you have a document structure that fits together conventionally, you will find that simply sending undo invocations to either the view's undoManager or the document's undoManager (they are the same object in fact), all undoable actions are consolidated. There's no real trick to it - it more or less just works. Only when you have a less straightforward/conventional object relationship between your view and the document might you need to do something more - which is why I'm wondering whether this is what your real problem is. In your code above, you have an outlet called "filesOwner", which looks a bit weird to me (again without knowing more about how you've structured it, it's hard to be sure). What you normally do is set the class of the NIB's filesOwner proxy to be General/MyDocument, or whatever you've called it. You don't name an outlet and make this connection yourself. --GC

----

I created it as a way of getting to the General/MyDocument object from my custom view.  I just copied the name from what Interface Builder was naming it.  I'd be happy to learn a better way to do anything I'm doing.

I started with an General/NSDocument Based app in Xcode.  What probably broke everything is that I'm using a window with a drawer on it.  So I deleted the main window in the nib and plopped a new one in...  The only setting I had to change was of course to tell the window to be visible from start up.  If I was supposed to set something else then that's the issue.

The _nextResponder ivar in the debugger is set to an instance of General/NSClipView...  Not sure if this is good/bad/indifferent.  It's in a scroll view, so this doesn't surprise me.

Thank you guys so much for your time and attempts to help me.  What other info can I provide about what I'm doing?  I'd be happy to provide copies of the current project if someone with more knowledgable eyes wants to poke around.

UPDATE: I connected the initialFirstResponder of the window in the interface builder to my custom view and nothing appears to be different.

----

Have you got a connection in the NIB file from filesOwner's "window" outlet to your window? Having one going from your view to the document is OK, though it won't automatically mean anything to the document object. Sounds like there's no connection there, which is why your window doesn't show up if you haven't set it to be visible on start up. Normally, you don't set this, and rely on the document showing it for you. The window has to be "owned" by the document. Even though it's in the same NIB, unless you connect it, it's not automatically assumed that it has anything to do with the document.

You don't need to delete the window to make a drawer, but let's not cloud the issue here by worrying about that for now. To follow the chain of responders, you'll need to go further than just the first one. It will be something like view->clipView->scrollView->window->document. Actually I think that there isn't an explicit link through _nextResponder from the topmost view to the window - but there is a 'window' ivar at that point. I'm guessing that internally the next responder chain is implicit at that point - the top view will always forward commands and events to its window. Its not unusual for frameworks to do this sort of thing, though without source code for Cocoa there's no way to know for sure. So this advice may not be all that helpful - however if you can't find a connection from the window to the document, that may confirm your problem. (Easiest to check in the NIB first though). --GC

----

Indeed, that was the issue.  The filesOwner's "window" outlet wasn't connected to anything.  As soon as I connected that up everything started working as expected.  I now have the circle appearing in the close box as well.  Thanks so much for all your help!

----

Cool :) Nice to know the system works, occasionally! --GC.
