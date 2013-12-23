---
layout: page
title: AccessingNSApplicationUsingPID
---

Hello,

I'm looking for a way to get all the windows of all the running apps.

I know how to get all the windows of my app:
NSArray *windows = [NSApp windows];

I also know how to get the list of the running apps:
NSDictionary *dictionary=[[NSWorkspace sharedWorkspace] 
activeApplication];

However, NSDictionary returns NSStrings with the app names, and 
NSNumbers with process ids, etc. It doesn't return NSApplications to 
use like [NSApp windows]. I'm not sure how to use the NSDictionary 
information to ask for the list of windows for each application. Any 
help is appreciated.

Thanks.

Goran

----

This is not possible. What are you trying to do? --FinlayDobbie

