---
layout: page
title: FileUpdateNotifications
---

Does anyone know what the notification name is for when one app updates a file that is also open in another app (i.e. when you edit and save source in TextEdit and then click on a text view in PB of the same file, PB will display the changes that were made in TextEdit).

When TextEdit saves a file, it sends a distributed notification through the NSDistributedNotificationCenter with the name "com.apple.carbon.core.DirectoryNotification", but the object associated with this notification is set to nil. Does anyone know how to get more information about events that involve directory notifications?

OK, I've figured out a way to notify all running apps that a file has been modified by:

    

id sharedWorkspace=[NSWorkspace sharedWorkspace];
[sharedWorkspace noteFileSystemChanged:filePath];



Now the only thing I need to figure out is a way to get a running app to automatically update itself. I'm writing a command line tool that edits source files that are open in Project Builder, and the code above will notify the sharedWorkspace that the openned file in PB has been changed. The only problem is that Project Builder will only check to see if files have been updated when the application is brought back into foreground (i.e. clicking on TextEdit and then clicking back on PB). I could waste some more time by trying to figure out how to execute an AppleScript to trick PB into thinking that it is being brought back into foreground, but there has to be an easier way to do this. BTW, the command line tool is being executed within PB using CONTROL+"r".

