---
layout: page
title: SheetProblemsWithNSTableView
---

I have a document based application where each document consists of several windows that all have drawers. Each drawer contains an NSTableView with two columns, ID and Name, and lists resources of a certain kind from the resfork of the document. When one of the items is clicked, it loads that resource's data into the window. Now the data can be edited and saved. If the data isn't saved and another resource in the list is clicked, the desired behaviour is this:

1. In selectionShouldChangeInTableView:, the unsaved status is recognised and a sheet or panel appears asking to save. If Save, the data is saved and the selection is allowed to change. If Don't Save, the selection is allowed to change. If cancel, selectionShouldChangeInTableView: returns NO. 

I sought to implement this in a number of ways but in all cases there are problems. If the sheet or panel is run modally (using beginSheet:.. followed by runModalForWindow:), there are two problems. Firstly, if the selection is allowed to change, the NSTableView doesn't recognise it - eg. visually the selection may be row 2 but the tableview thinks it's row 7, and neither the notification (NSTableViewSelectionDidChangeNotification) nor the action method are sent to their targets.

If the selection cannot change (selectionShouldChange... returns NO), the panel repeatedly appears (ad infinitum). In the case of a sheet, two sheets appear instead of one. 

Ideally I would like to use beginModalSessionForWindow:relativeToWindow: but it is deprecated. The real problem is that selectionShouldChange... returns a BOOL and without a modal session you cannot get a return value within the function, and with a modal session you have the problems discribed above.

Is it possible to get around this? It seems ridiculous that the NSTableView isn't recognising it's own changes - if you use the modal method, try to change the selection and then click save, the selection visually changes, but the delegate/action methods are not called, and if you output the [aTableView selectedRow] it produces the wrong number! Then if you click in the tableView, it suddenly sends the notification and action messages for the last event! What is going on!

Cheers!

Kane O'Donnell

----

Does anyone know anything about this?  I'm experiencing the exact same problem.

Thanks,
Joe

----

Let me make sure I understand you:


* The user selects a row in a table, and the window displays information about the resource that row represenets.
*The user changes some information in the window
*The user attempts to click a different row, selectionShouldChangeInTableView: is called, and your implementation of it pops up a sheet


I think the problem is that     selectionShouldChangeInTableView: needs to return immediately, and you're not doing that. Your code can't pop up a sheet and wait for the user to click Save or Cancel before returning YES or NO. If you do need to wait for something else to happen (the user to respond to a sheet), you must return NO to disallow the selection change right now, and then programatically change the selection when you've gotten a response from the user.

Inside     selectionShouldChangeInTableView:, do this:


* See if the data has been edited. If not, return YES and allow the table's selection to change.
* Record the row that the user is trying to select in a variable somewhere
* Pop up the sheet asking the user if they want to save.
* Return NO -- the table's selection cannot change yet, because we have to ask the user something first.


When the sheet ends and your     sheetDidEnd: method is called:


* If the user clicked Cancel, don't do anything. (The selection has already been prevented from changing.)
* If the user clicked Save, save the current resource, and then change the table view's selection to the row that you recorded in step 2 above. This will complete the user's action.
* If you wish to have a Don't Save option, then just change the table's selection without saving the current resource.


The key point is that you have to stop in the middle of the user's action to pop up a sheet. You must cancel the action, and then programatically complete the rest of it once you've gotten a response from the sheet. That means storing information about what the user is trying to do (in this case, the row they're attempting to select) so that you can use that information later. You can store the row in an intance variable, or in an NSNumber that you pass along as     contextInfo to the sheet, and the sheet passes back to you when it ends. Just as long as you record it, and can read it again when the user selects an option on the sheet.

----

BTW, I think the reason that you're seeing strange behavior with the table's selection is because you've stopped the     selectionShouldChange: method in the middle of execution. It's still running (sitting on the stack, waiting for your modal event loop to finish) but the application is sending new events to the table, so the table view is now in a very weird, inconsistent state. I don't think any of NSTableView's methods are designed to be used under those conditions.

----

In short, avoid modality at all costs. If you really need to do something modally, open up a brand new window and make it modal. Or, better yet, use     NSRunAlertPanel().

----

Thanks for the discussion.  My implementation is displaying a sheet and then running a modal session for the sheet (so you assumed right).  I had previously used an NSRunAlertPanel() implementation instead of a sheet to confirm saving and that worked fine (and then I decided that I wanted a sheet...).  I have also implemented selectionShouldChange: method as you described.  Returning NO immediately and passing the row number as contextInfo.

With that implementation, I ran into some trouble otherwise.  I had everything working correctly when a new row was selected and there was data to save.  My next question, however, was, "how do I implement something the same (a confirmation sheet) when the user tries to close the window and changes need to be saved?"  And also, "What if changes need to be saved as the program is being closed?  Need I have 3 different methods to display a sheet?  With 3 different sheetdidEnd: selectors?"

Thanks again.  The new questions I've posed don't need any response unless there is a simpler way of doing things. :-). - Joe

----

[NSNumber numberWithInt:rowIndex] -- when passing this as contextInfo to NSBeginAlertSheet, does this need NSNumber need to be retained in the call and released in the didEndSelector? 

Thanks, Joe

*In general, if your contextInfo (or userInfo or whatever) is of type id, you can assume that Cocoa will properly retain and release it. If it's of type void *, Cocoa can't be sure that it's an object, and so Cocoa will not retain it. In this case, contextInfo is void *, so it's up to you to retain it until you're done with it.*

----

I do not think modality is the issue here. I am having the same problem. Just for a test, at the top of my selectionShouldChangeInTableView method I put
NSLog(@"returning NO");
return NO;

Every time I try to select a row, it prints returning NO twice. Something is causing selectionShould... to be called again when NO is returned. Any thoughts?

----

Same problem here. I've googled, I've #macdev'd and can't seem to find any answers to this problem. As inconvenient as it is, we might just have to hack around it.

----
Instead of a nebulous "something is causing...", why don't you run the thing in the debugger and find out *what* is causing it?


----

It seems that selectionShouldChange is called continuously while the mouse button or key is pressed. I.e. if you just put a debug print statement in it and always return NO, try holding the mouse button or key on a different table row and observe debug statement being printed over and over again. So my theory is that the control manipulation that opens the sheet above (mouse click or button push) is long enough to queue two calls to selectionShouldChange method. It is really confusing, however, because in the sequence of calls I see in the debugger, it goes through keyDown or mouseDown method, which definitely gets called only once. 

-- Dimitri

