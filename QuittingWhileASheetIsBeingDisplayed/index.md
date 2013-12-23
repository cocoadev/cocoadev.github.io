---
layout: page
title: QuittingWhileASheetIsBeingDisplayed
---

see also DisplayingSheets

When a sheet is displayed, you can�t quit. Why isn�t the Quit menu item disabled, then??

Is there a way to handle the terminate event, dismiss the sheet and quit the app?

--wdyp

----

I am pretty sure that behaviour is designed to stop people losing data. I wouldn't mess with it as you may confuse users.

----

I agree with you, but in such a case, the Quit menu should be disabled!! I didn�t succeed in disabling it by hand either. How can I do this?

----

1) My understanding of the beginSheet method is that normally it has a modalForWindow argument.

2) IF the sheet is running modally, why would your Quit menu NOT be disabled?

3) In my applications, I have never seen the Quit menu disabled in such circumstances without that state being visually confirmed.

----

1) Yes it has a modalForWindow argument.

2) I agree with you, but the fact is my Quit menu is NOT disabled!

3) I don�t understand "without that state being visually confirmed".

Could you explain this?

--wdyp

----

I just mean the usual UI feedback you get, where the item is dimmed if disabled.
The example I am checking in my own app is a modal sheet opened from a menu command.

In the action, the sheet opens with:

    
	[ NSApp beginSheet: controlsPanel 
		modalForWindow: mainWindow modalDelegate: nil
                didEndSelector: nil contextInfo: nil ];
	[ NSApp runModalForWindow: controlsPanel ];
	[ NSApp endSheet: controlsPanel ];
	[ controlsPanel orderOut: self ];


There is a button in the sheet to put it away. The button triggers another action:

    
	[ NSApp stopModal ];


This is all the code that is required to handle opening and closing my sheet. All it needs is an outlet in IB to the panel that is used to
contain the sheet's controls. There isn't even a modal delegate. And it definitely disables my Quit menu item.
By the way, the application is not document-based.

If your code is functionally different from the above, you should post your code here so that
somebody smarter than I am can figure out what is wrong.  I hope you find this helpful.

----

OK, thank you, that�s helpful! Here�s the code I have written:

    
-(IBAction)beginPrefsSheet:(id)sender {
    [NSApp beginSheet:prefsWin 
       modalForWindow:mainWin 
	modalDelegate:self 
       didEndSelector:nil
	  contextInfo:nil];   
}

-(IBAction)endPrefsSheet:(id)sender {
    [prefsWin orderOut:sender];
    [NSApp endSheet:prefsWin];
}


So I guess I�ve got to set the modal delegate to nil. But I don�t understand why there are [ NSApp endSheet: controlsPanel ]; and [ controlsPanel orderOut: self ]; in your beginSheet action. Is it that the modal thing and its end are set up at once, and then the stopModal function is enough to end the sheet?

I tried your way and it works!

I don�t find it very natural to tell the sheet to order out in the beginSheet action, though.

The big problem is that it disables almost all menus: I�ve got an edit contact sheet that normally allows to use the Email this person menu to be choosen, so I�ve got to choose between two options:



* Can email but Quit is enabled but beeps if choosen;     

*That's what I mean; it's NOT enabled, because it beeps. It's just not dimmed.*

* Quit disabled, no beep, but can�t email a contact while the sheet is on.

*Why do you want to implement a capability like this as a sheet? It is certainly not necessary to do so.*



So here is my question again: I want to be able to enable/disable menu items **as I want**, how can I do this with the Quit menu?

----

*not if you use a modal sheet*

----

Do you mean that it simply isn�t possible?

--wdyp

----

Ordinarily, a Cocoa application can not be quit by the user as long as one or more sheets are visible.  This default behavior makes sense for sheets that are needed to make sure the application quits gracefully.  For example, if a document has a "Save" sheet visible when the user attempts to quit the application, there is a good chance the user would really like to finish saving the document before the application quits.

However, the iWork applications display a document template selection sheet.  The fact that a user has not yet selected a template indicates that no document information will be inadvertently lost if the application is quit before the user has responded to the sheet.  So you do you implement a minimal Cocoa document based application that displays a sheet attached to each new document right after the new document is displayed.  Quitting the application while the initial sheet is displayed should work.  Quitting should still prompt the user to save edited documents and should even close the initial sheet to display the Save sheet as necessary.


Step 1) Create a Cocoa document based application.


Step 2) Implement code to display a sheet on each newly created document window.

   Add an action like - (IBAction)closeMyCustomSheet:(id)sender; to close the sheet.
   See http://developer.apple.com/documentation/Cocoa/Conceptual/Sheets/Tasks/UsingCustomSheets.html


Step 3) Enable Quit while sheets are displayed.

   By default, the Quit NewApplication menu item sends the terminate: action message to the File's Owner in the main Interface Builder file.  The File's Owner in the main Interface Builder file is always an instance of NSApplication (or a subclass).

   Disconnect the Quit menu item's action from File's Owner terminate: and connect it instead to First Responder which convenienetly already has a terminate: action.


Step 4) Add the following code to your document class:

    
- (IBAction)terminate:(id)dummy
{
   [[NSApp orderedDocuments] makeObjectsPerformSelector:@selector(closeMyCustomSheet:) withObject:nil];
   [NSApp terminate:dummy];
}


   This implementation of -terminate: closes all instances of your custom sheet that may be currently displayed and then sends the terminate: message to the application's single NSApplication instance just like the Quit menu item used to do before you rewired it.

   Now, when you quit the application, any visible custom sheets are automatically closed.  The application then prompts you to save any unsaved documents.

----

I've tried the above suggestion & it works almost perfectly. The application quits by clicking "Quit" on the app menu and through the Cmd+Q shortcut, but the red "close" button on the window is still deactivated while a sheet is being displayed. Any ideas on how to activate it? (it may not be possible, though, since Apple has not done this in iWork). 

--Alex

