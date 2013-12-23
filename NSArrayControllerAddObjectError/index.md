---
layout: page
title: NSArrayControllerAddObjectError
---

I'm trying to do this:

    
[anNSArrayController obj];


And I'm getting this error:

    
2007-04-25 17:46:01.017 SimpleBrowser[4967] *** NSTimer discarding exception **** 
-[NSKeyValueSlowMutableArray insertObject:atIndex:]: value for key recentShows 
of object 0x346030 is nil' that raised during firing of timer with target 346030 
and selector 'updateRecentShows:'


But I'm not sure which obj it's complaining about. I can see that 'obj' is not nil in this case.  Anyone know??
----
Please explain     [anNSArrayController obj], since NSArrayController has no method "obj".

