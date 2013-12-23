---
layout: page
title: SavingDataInCoreDataNonDocumentApps
---




I'm writing a non-document-based CoreData application and it's coming along beautifully. The problem I'm seeing is that a user could spend a very long time manipulating and modifying their data, but it appears the data is not saved until the application is quit.

I know I can explicitly tell my managed object context to save via     NSManagedObjectContext's     -save: method. However, I know that pending edits will be / need to be committed first, which clears the undo stack (doesn't it?).

What I'm looking for is the best-practice advice on handling this situation where many 'editor windows' can be open on various objects, the user could be typing (which makes timed auto-saves probably a bad idea) and may want to undo their changes (because nobody's perfect). :-)

Your thoughts?

----

**Discussion**

I've been having a similar problem. They way I got around it was described in CoreDataSavingIndividualChanges.

Basically you implement a single NSPersistentStoreCoordinator in the app delegate, then each window that requires access to the data can implement it's own managed object context and undo manager. This way also means you get an undo manager for each window. The main caveat is that you *must* use the NSSQLiteStoreType to maintain some sort data integrity. Also, there's the possibility that changes made in different windows could conflict.

----
*Thanks, but that's not quite what I'm after. I guess my question (after thinking about it awhile) is more like this: Since a non-document-based application doesn't really "represent a file" in the 'document' sense, should there be a File menu (like Save, etc.)? I mean, there's no "Open", etc. The default Core Data Application template in XCode sets it up to save automatically on quit. What's the standard practice in this scenario?*

----
The easiest way is to have an NSTimer, dunno if that would be 'standard practice'. This is what I do, calling 'save' every min.
The pb is every time you save, the undo stack is gone, which you would notice happen in document-based app. In my case, I don't care about undo. I have read smth about the issue somewhere, but could not find it (spending 5-10 minutes on it). Maybe on this site...
--CharlesParnot

----
My thought is really what is the difference between a "non document" application and a document based one where you only have one document open?  I'd provide a Save menu item that performs the save, thus allowing the user to commit the changes made knowing and controlling when the undo history is reset, consistent with the user experience from any other document based application.

The deeper question though is why not use the multiple document architecture on Cocoa?  If it were a Windows application I could understand.  With Cocoa you get the functionality almost for free so why change the user experience for your application even if you don't intend for it to be used that way?

----
For the same reason individual calendars aren't saved as separate 'documents' (files) and displayed in separate windows in iCal. One user, one data store. In those situations, app-versus-document is The Right Thing.

----
D'oh of course.  Doesn't CoreData already use transaction logs with a SQLLite document?  If so it probably wouldn't be too much to ask of Apple to have a mode where any uncommitted changes are actually on the disk in the form of a transaction log.  Therefore like any other database application there is no "save" other than to commit the transaction log into the database.  The second part of that is to have the undo manager not forget its changes if that log is committed.

----
The CoreRecipesCocoaBindings tutorial shows an elegant solution to the problem. In IB select the "Save" menu item and in the Inspector make the following binding on "enabled" (it's under the subheading Availibility):
-- Bind to: YourProject_AppDelegate
 -- Model key path: managedObjectContext.hasChanges

This will cause the menu item Save to perform a saveAction: to the ManagedObjectContext whenever it is activated.

----
http://developer.apple.com/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/Reference/Reference.html#
//apple_ref/occ/instm/NSManagedObjectContext/hasChanges

hasChanges isn't KVO compliant.

----
If you are targeting 10.6 hasChanges **is** KVO compliant.
'On Mac OS X v10.6 and later, this property is key-value observing compliant'

