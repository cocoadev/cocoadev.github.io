---
layout: page
title: SegmentViolationDuringKeyEquivs
---



Okay - strangeness. I've got a document-based cocoa app here. The problem:

1 - Launch application
2 - Load previously-saved document according to my NSDocument subclass.
3 - Close document.
4 - Use keyboard equivs to do ANYTHING (Cmd-Q, Cmd-H, Cmd-N, etc) and I get a signal 11.
4a - Close the document with the mouse and using the mouse to activate menu options works just fine. ONLY when I use equivs after closing a saved doc does the problem appear.

  Yet if I create a new document without saving it, key equivs work fine. Obviously it's my error, but I've removed the 'release' and 'autorelease' from every object in the ENTIRE PROJECT to make sure I'm not prematurely releasing anything. Every object I create is stuck in memory, so I've got no idea what's happening here. Could something be wrong with my MainMenu.nib?

  Thanks for any help.

----

Is this a ZeroLink build? Have you tried cleaning the project?

----

I've since tried replacing the MainMenu.nib. No luck. Yes, I've cleaned the project and it does the same thing with zero-link enabled and disabled. :-( I think I'm probably doing something very stupid with memory management.

*Can you run your app in the debugger and find out exactly where the SIGSEGV is happening?*

handleKeyEquiv event. None of my own objects or methods are in the stack. :-(

    
#0  0x90831510 in _class_lookupMethodAndLoadCache ()
#1  0x90831298 in objc_msgSend ()
#2  0x90831298 in objc_msgSend ()
#3  0x92ed7c50 in -[NSApplication _handleKeyEquivalent:] ()
#4  0x92df53dc in -[NSApplication sendEvent:] ()
#5  0x92dfdc54 in -[NSApplication run] ()
#6  0x92eba2b8 in NSApplicationMain ()


----

Okay, here's an update: I've isolated the problem to my table datasource. The datasource asks it's owner document -document (which points to the NSDocument subclass for my project and works fine while the document is open) for the numberOfRowsInTableView ... Again, while the document is open, this works fine. Well, if I a) hard-code it to return 0, or b) disconnect the table datasource outlet in IB, the problem goes away (but of course I have no data in my table). If I comment out the tableView:objectValueFor... and setObjectValueFor ... methods (returning nil on the former), it has no effect. 

SO ... the code in my datasource that asks its owner document for its array of objects and asks that array for its -count is apparently being called after the document window is closed, but not until I try to execute a key equiv.  On a hunch, I tried opening a saved doc, closing it, then opening the same one (to make sure there's a document there) and trying a key equiv -- no luck. 

I can only surmise that for some reason, the menu / first responder is holding onto something (my doc subclass, its array of objects, or an object in that array ... SOMETHING). 

   H E L P ! !

----

Hard-code *what* to return 0, sorry?

Verify that any methods you've written that need to call their superclasses do so, and more generally that all code that should be called when your document closes is called. Then try dropping a tracepoint in somewhere and seeing exactly what happens when you use a menu item. 

*Hard code the datasource method     numberOfRowsInTableView to return zero - he stated it above.*

----

Random guess: in the     -dealloc method of the class that's your table's data source, do     [table setDataSource:nil]. The idea is that the data source is disappearing before the table, and havoc breaks out when the table tries to query the data source after it's disappeared. Setting the data source to nil will ensure that the table never tries to access the data source after it's gone.

