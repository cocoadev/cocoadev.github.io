---
layout: page
title: OpenDrawerWithDragAndDrop
---



What I have is an NSTableView that represents an array of Foo objects. This is in one window. In another window, I have an editor of Bar objects, with a drawer to received dropped Foos, which links many Foos to one Bar. I have a Foo pasteboard registered and drag-n-drop works just fine when a Foo is dropped on the receiving table in the drawer. There can be an arbitrary number of Bar editor windows open at once but only one window containing Foos.

What I *want* is the following: When a user drags a Foo to a Bar editor window and the drawer is not open, I want the drawer to automatically pop open when the Foo object enters the bounds of the Bar editor's window (popping it closed again when the operation completes if it wasn't open in the first place, to be nice to the user).

In Mail.app, the mailboxes drawer auto-opens as soon as the drag is initiated from the message table, but this won't work in my case because the destination window won't be known until the user drags over it. For that matter, I'll need to auto-close the drawer on drag out if there's no drop (say, the user whizzes by one window on the way to another).

What's the best way to approach this? In other words, which object should be paying attention to the current whereabouts of the Foo object during the drag? Since I have an NSTextView covering most of the window, making the window itself react to the Foo entering its bounds doesn't seem right and it seems very Un-Cocoa-Like (TM) to make the window *and* the text view both pay attention, etc. So ... any ideas?

----

Windows aren't the dragging destination of drag and drop operations, views are. If you want to have the dragging destination view do stuff when something is dragged inside of it (like open a drawer on its window), you can use the draggingEntered: and draggingExited: methods in the NSDraggingDestination protocol to do that. You'll have to write a custom view class to act as the dragging destination. If an NSTextView is covering your window, I'd suggest subclassing NSTextView and tweaking draggingEntered: and draggingExited: there. Or, if you have a bunch of empty space on the window, create your own NSView subclass and make it the window's content view.

You could also approach this from the other side: the dragging source. When the user begins a drag from a view (the dragging source), it could programatically pop open all of the drawers on all of the windows. It would have to record which drawers it opened (in an instance variable, perhaps), and then close them when the dragging operation completed (see the NSDraggingSource protocol for hooks to do that).

----

Incorrect. Well, partially. NSWindow can be subclassed and the NSDraggingDestination protocol can be implemented. Just make use of draggingEntered, draggingUpdated, and draggingExited to make the whole darned thing respond to a drag operation. I've done something similar. To make it nice, I remembered the drawer state. If the drawer was opened by the draggingEntered, I created a timer that got set up and fired on draggingExited so that the drawer would close after a second if it didn't get notice that the drag op was now over the drawer. If it *was* over the drawer, the drawer sent notice saying "hey, hold on there." Once the drag op completed, the window is notified and if its drawer was opened by a drag op, it closes again immediately.

----

See http://www.stone.com/The_Cocoa_Files/Ins_and_Outs_of_Drag_and_D.html for howto subclass NSWindow to do this. 

There might be a HCI problem with dragging a file onto a window (as opposed to a widget, such as an "Input file" NSTextField), and I would be tempted not to do for applications intended for release. (It is quite a good interface for private applications, where dropping onto a window means: Now, do the the self same thing with this file"). There are cases when ones data model (Document Controller) would benefit from dropping a file onto a window (as opposed to a view within a window), but you would probably have discovered this during design. An example of an appplication where dropping into a window makes sense is a web browser, where dropping an HTML file onto a window means replace the page with the render of the contents of this file.

IMHO, if you need to do this (and I have just done it), you should not subclass NSWindow, but add methods to the window's delegate. This is described around page 714 of BookCocoaProgramming.

