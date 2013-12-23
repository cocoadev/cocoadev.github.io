---
layout: page
title: UKKQueue
---

General/ObjectLibrary

"A wrapper class around the     kqueue(2) file change notification mechanism.

Simply create a General/UKKQueue, add a few paths to it and listen to the change notifications via General/NSWorkspace's notification center."

http://www.zathras.de/programming/sourcecode.htm#General/UKKQueue

See also: General/FileSystemNotifications

----

How can I subscribe a directory situated in a removable media?

----
The file system has to support the change notifications.  Things like NFS and FAT32 don't have the necessary support to do that (as far as I know)

----
If the file system does not support kqueues, you can use the traditional General/NSWorkspace notifyFileSystemChanged and corresponding notifications to make things work more or less right. You can also do polling if needed. -- General/EmanueleVulcano aka l0ne

----

General/UKKQueue works quietly, politely, and as advertised. I used it in a recent project and was very pleased to have done so! -- General/RobRix

----
I'm trying to use this, but don't seem to be getting any notifications. Could anyone help?

I'm setting up the General/UKKQueue as follows.
    
General/UKKQueue* kqueue = General/[UKKQueue sharedFileWatcher];
    General/NSLog(@"adding dir=%@", [Preferences tvShowDirectory]);
    [kqueue addPathToQueue:[Preferences tvShowDirectory]];
    General/NSWorkspace* workspace = General/[NSWorkspace sharedWorkspace];
    General/NSNotificationCenter* notificationCenter = [workspace notificationCenter];
    General/NSArray* notifications = General/[NSArray arrayWithObjects:
        General/NSWorkspaceDidPerformFileOperationNotification,
        General/NSWorkspaceMoveOperation,
        General/NSWorkspaceCopyOperation,
        General/NSWorkspaceLinkOperation,
        General/NSWorkspaceCompressOperation,
        General/NSWorkspaceDecompressOperation,
        General/NSWorkspaceEncryptOperation,
        General/NSWorkspaceDecryptOperation,
        General/NSWorkspaceDestroyOperation,
        General/NSWorkspaceRecycleOperation,
        General/NSWorkspaceDuplicateOperation,
        nil];
    
    int i;
    for (i=0; i<[notifications count]; i++) {
        General/NSLog(@"Adding notification=%@", [notifications objectAtIndex:i]);
        [notificationCenter addObserver:self selector:@selector(fsHandler:) name:[notifications objectAtIndex:i] object:nil];
    }


General/FsHandler never gets called when I create/touch/delete files within the added file structure.  Any suggestions?  Thanks!


----

General/UKFileWatcher/General/UKKQueue doesn't use those constants as its notification names. See http://www.zathras.de/programming/cocoa/General/UKKQueue.zip/General/UKKQueue/General/UKFileWatcher.h for appropriate notification names.

----

Yes it always helps to subscribe to the proper notifications. ;)
Thanks! Worked like a charm.

----

General/UKKQueue was written in 2003 and is a bit long in the tooth. There is now a modern, faster, more efficient class that you might consider: General/VDKQueue. You can find it here: http://github.com/bdkjones/General/VDKQueue
