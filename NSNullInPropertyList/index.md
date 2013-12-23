---
layout: page
title: NSNullInPropertyList
---



I just had a bug where I was using NSNull in a property list and successfully writing this list to a file, but now I'm getting an "Invalid Property List" error. I searched Apple's developer mailing lists and found that some people are saying that NSNull is a valid plist entry. I guess this is not the case. 

This...
    
    NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:[NSNull null], @"null", nil];
    NSString *error = nil;
    NSData *xmlData = 
        [NSPropertyListSerialization dataFromPropertyList:dictionary 
                                                   format:NSPropertyListXMLFormat_v1_0 
                                         errorDescription:&error];
    NSLog(@"\nplist: %@\nData(%i): %@\nerror: %@", dictionary, [xmlData length], xmlData, error);


Logs this...
    
2007-09-07 09:10:49.329 test[10629] 
plist: {null = <null>; }
Data(0): (null)
error: Property list invalid for format


----
That would be because NSNull is *not* a valid plist type. Apple has a full list of valid plist types and NSNull is not on it.

