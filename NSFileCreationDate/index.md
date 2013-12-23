---
layout: page
title: NSFileCreationDate
---

Hello,

how can i find the creation date of a file? i know that i need to use NSFileCreationDate, but i am not sure how to tell it which path, and then convert the NSDate to NSString.

- jeremy

----

I can help you with the last one. On the NSDate page, follow the link to Apple's documentation and look around. (Hint: it's the -description set of methods.)

----

Here's some sample code.  It's largely stolen from Apple's documentation on NSFileManager.  Let's say you want to check something on the file ~/Documents/Contract.rtf

    
NSString *path = [NSString stringWithString:@"~/Documents/Contract.rtf"];
NSString *fixedPath = [path stringByStandardizingPath];
NSFileManager *manager = [NSFileManager defaultManager];
NSDictionary *dict = [manager fileAttributesAtPath:fixedPath traverseLink:YES];
NSDate *fileCreationDate = [dict objectForKey:NSFileCreationDate];
NSString *fileCreationDateString = [fileCreationDate description];

----
    
Slightly messier but more compact:

[[[[NSFileManager defaultManager] fileAttributesAtPath:[[NSString stringWithString:@"~/Documents/Contract.rtf"] stringByStandardizingPath] traverseLink:YES] objectForKey:NSFileCreationDate] description];




If you do it this way, fileCreationDateString will look like "2001-03-24 10:45:32 +0600".
If you want it output in some other, perhaps prettier, format, you'll need to use NSDate's
**descriptionWithLocale:** or **descriptionWithCalendarFormat:timeZone:locale:**

----

thanks for the great help! i have it working perfectally!

- jeremy

