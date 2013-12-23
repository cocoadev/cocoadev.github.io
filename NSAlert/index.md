---
layout: page
title: NSAlert
---

New class in 10.3 encapsulating alert panels.


[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html]

You use an NSAlert object to display an alert, either as an application-modal dialog or as a sheet attached to a document window. The methods of the NSAlert class allow you to specify alert level, icon, button titles, and alert text. The class also lets your alerts display help buttons and provides ways for applications to offer help specific to an alert.

----

Also see:

[http://developer.apple.com/documentation/Cocoa/Conceptual/Sheets/Tasks/UsingAlertSheets.html]

from which comes this sample:

    
NSAlert *alert = [[[NSAlert alloc] init] autorelease];
[alert addButtonWithTitle:@"OK"];
[alert addButtonWithTitle:@"Cancel"];
[alert setMessageText:@"Delete the record?"];
[alert setInformativeText:@"Deleted records cannot be restored."];
[alert setAlertStyle:NSWarningAlertStyle];
[alert beginSheetModalForWindow:[searchField window] modalDelegate:self didEndSelector:@selector(alertDidEnd:returnCode:contextInfo:) contextInfo:nil];


----

A gotcha to note:  NSAlert became public in 10.3, but there was a private class named NSAlert in 10.2 as well, and it didn't have the same interface!  So be careful when you're trying to write backwards compatible code.  It will not suffice to check for the existence of a class named NSAlert.

----

That and there is no real reason to use NSAlert in the first place. Just use the function NSRunAlert et al.

----

Apple sez:

*The NSAlert class, which was introduced in Mac OS X v10.3, supersedes the functional Application Kit API for displaying alerts (NSRunAlertPanel, NSBeginAlertSheet, and so on). The former API is still supported, but you are encouraged to use the NSAlert class for your application�s alert dialogs.*

----

Its a tough call, compatibility, or simplifying your life.

----

There is a 'compatibility constructor'

    
+ (NSAlert *)alertWithMessageText:(NSString *)messageTitle
                    defaultButton:(NSString *)defaultButtonTitle
                  alternateButton:(NSString *)alternateButtonTitle
                      otherButton:(NSString *)otherButtonTitle
        informativeTextWithFormat:(NSString *)format, ...


of which the docs say *This is a compatibility method. It is designed for easy adoption by applications migrating from the corresponding functional API. If you are creating an alert for the first time, it is recommended that you allocate (alloc) and initialize (init) the object, and then set its attributes using the appropriate methods of the NSAlert class.*

It would be trivial to write your own constructor using Apple's recommendations.

----

Be aware that if you use the compatibility constructor and call runModal, it will return different values than if you create it via alloc / init. This can easily introduce bugs with alerts that are only used in rare cases, so it's a good idea to always use alloc / init, even if it is a few extra lines of code.

MarcCharbonneau


----
Looking to add a checkbox to a standard NSAlert box?: NSAlertCheckbox


----
I would like to display a modal alert sheet right after my window did load. so I put the following code in the awakeFromNib method:
    
NSAlert *alert = [[NSAlert alloc] init];
    [alert addButtonWithTitle:@"OK"];
    [alert setMessageText:@"Delete the record?"];
    [alert setInformativeText:@"Do you want to delete the record?"];
    [alert setAlertStyle:NSInformationalAlertStyle];
    [alert beginSheetModalForWindow:myWindow modalDelegate:self didEndSelector:@selector(alertDidEnd:returnCode:contextInfo:) contextInfo:nil];

The sheet is displayed but it is not modal. It looks like modal (does not have titlebar) but is not attached to the window.
It works if I put the same code in an IBAction method called by a button, so i assume the window is not loaded yet when i try to open the sheet. but i thought awakefromNib is only called when everything is initiallized...
What's my problem?

----
Better to put your code in a appDidFinishLaunching: method as myWindow may not have completed all of its initialisation (or be visible) at the awakeFromNib point in time.

----
I had the same problem until I added:

[window makeKeyAndOrderFront: nil];

before creating the NSAlert.

