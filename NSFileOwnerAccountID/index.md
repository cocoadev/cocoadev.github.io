---
layout: page
title: NSFileOwnerAccountID
---

see UserID

----

In case anyone was interested in finding out the UID of a **file** rather than the current user, try this:

    
NSFileManager * fileManager = [NSFileManager defaultManager];
NSDictionary * attributes = [fileManager fileAttributesAtPath: @"/cleanup at startup" traverseLink: YES];
NSNumber * owner = [attributes objectForKey: NSFileOwnerAccountID];
NSString * ownerName = [attributes fileOwnerAccountName];
unsigned long posixPermissions = [attributes filePosixPermissions];

NSLog(@"owner = %@", owner);
NSLog(@"ownerName = %@", ownerName);
NSLog(@"posixPermissions = %lu", posixPermissions);

