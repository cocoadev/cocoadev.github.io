---
layout: page
title: FindAppIdentifierFromName
---

is there a way i could find the identifier for an application by it's name.  i.e. "iCal" would return com.apple.ical or whatever.

thanks!
----
NSWorkspace has a lot of system-user methods. In this case, you probably want to do something like this. --JediKnil
    
NSString *identifier = [[NSBundle bundleWithPath:[NSWorkspace fullPathForApplication:@"iCal"]] bundleIdentifier];

