---
layout: page
title: PostNotificationProblem
---

Hmm, one thing I just came across, if you post a notification with [NSNotificationCenter postNotificationName:object:userInfo:], what will the notification center do with the dictionary you give as a value for the userInfo parameter. I normally autorelease those objects, but now I get memory errors during the autorelease phase of the notifications. I looked through the documentation and the internet, but I couldn't find anything about it. If I don't release the dictionary, it will work, of course. Anyone out there with deeper knowledge about it?

--ThomasSempf

Please show the code. The retain/release semantics for this are the same as for everything else. If you alloc it, you need to release it. If you don't, you must not. NSNotificationCenter will retain what it needs.

----

From [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSNotificationCenter.html]:

*An NSNotificationCenter object delivers notifications to observers synchronously. In other words the postNotification: methods do not return until all observers have received and processed the notification.*

The notification center doesn't retain anything, AFAIK - it just passes the userInfo along to the observers, who are responsible for retaining whatever they're interested in. Are you releasing an already autoreleased dictionary?

----

Okay here is my code snippet:

    
//send notification to vaClient
NSMutableDictionary *tempDic = [[NSMutableDictionary alloc] init];
[tempDic setObject:root forKey:@"annotation"];
[tempDic setObject:[NSNumber numberWithLong:editedAnnotationPosition] forKey:@"position"];
[tempDic setObject:[NSNumber numberWithInt:[durationToolbarView viewWithTag:0] stringValue] intValue forKey:@"duration"];

[sessionNotificationCenter postNotificationName:kannotationChanged object:nil userInfo:tempDic];
//[tempDic autorelease];


I normally create my NSMutableDictionary with alloc and init, so I have to release it. But if I release it after the posting of the notification, I get the BAD_EXEC error.

--ThomasSempf

----

If you're using the userInfo somewhere else in the object that receives the notification, that'll happen if you release the userInfo dict in the sender. Try retaining it in the observing object (and releasing it in the object that posts the notification).

What's     root? When you release the dict, it will send a release message to its contents. What happens if you just say     [root release] as a test?

Also, does this dictionary need to be mutable for other purposes? It seems like you could just as easily use     [[NSDictionary alloc] initWithObjectsAndKeys:...] and avoid the overhead of mutability.

