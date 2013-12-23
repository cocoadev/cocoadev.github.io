---
layout: page
title: DoesDelegateExist
---

Hello,

I'm working on an app to be run on Tiger and Panther, and I'd like to test if my tableView delegate actually *recognizes* the "tableView:heightOfRow:" method.  This delegate is new to Tiger 10.4, so when my app is run on Panther I want to check and see that it isn't recognized and then respond accordingly.

Unfortunately, the "respondsToSelector:" method returns YES even when "tableView:heightOfRow:" isn't recognized because I do actually implement the delegate for when the app is being run on Tiger.

Does anyone know how to check if a delegate method is actually valid and recognized (instead of simply implemented)?

----
In Objective-C, "implemented" is nearly synonymous with "responds to". Can you explain why exactly you think this test is not sufficient, and what you're really trying to accomplish here? What does "valid and recognized" mean apart from "the object implements it"?

----

Let me see if I can be a little clearer...I know this sounds confusing...

In my app, I use the NSTableView delegate "tableView:heightOfRow:" to make a table view with variable row heights.  In Tiger this works great (this delegate is new in 10.4), but in Panther, since this is new in 10.4, it never gets called.

However, because I implement the "tableView:heightOfRow:" method in my code for use on Tiger, "respondsToSelector:" returns YES for this method, even on Panther (because it is implemented in my code for Tiger).

So what I am looking for is a way to find out if the "tableView:heightOfRow:" method exists in the NSTableView class (it does exist on Tiger where it is new in 10.4, and does not exist in Panther).  Does that make things any clearer?

In other words: **If the docs say a delegate is "New in 10.4", is there a way to test the delegate for existance to return YES on 10.4 and NO on 10.3?**

----

Either I do not understand what you are asking (most likely) or this is the most creative attempt to find platform versioning info I have ever seen.  ;-)

----

Haha, I basically wanted to test for specific features of the OS, but I suppose platform versioning would work - I was just thinking there might be a specific function to do the trick (but it sounds like there isn't).

Is there an easier way to find the OS version with Cocoa than what exists on the DeterminingOSVersion page?

----
    
if ([delegate respondsToSelector:@selector(tableView:heightOfRow:)]) {
    // delegate implements and will respond to that method
} else {
    // we must be on 10.3 or below
}


----

See, this is what I was trying to do, but the respondsToSelector: method returns YES even on 10.3 because I implement the "tableView:heightOfRow:" in my code to make it work for Tiger.  So that's why I was looking for something that would return NO when tested in 10.3 or below.
----
    
#ifndef NSAppKitVersionNumber10_2
#define NSAppKitVersionNumber10_2 663
#endif

#ifndef NSAppKitVersionNumber10_3
#define NSAppKitVersionNumber10_3 743
#endif

// returns true if the user is on tiger (or above)
+ (BOOL)isMacOSXTiger {
	return (floor(NSAppKitVersionNumber) > NSAppKitVersionNumber10_3);
}

// returns true if user is on panther or above
+ (BOOL)isMacOSXPanther {
	return (floor(NSAppKitVersionNumber) > NSAppKitVersionNumber10_2);
}



----
The original poster is badly misapprehending how delegates work and what it means to add a delegate method.

In the most basic form, a delegate is just an instance variable which is used like this:

    
...some code...
if([delegate respondsToSelector:@selector(...)])
   retval = [delegate ...];
...some more code, which may change behavior based on retval...


There is no master list which holds delegate methods. The list in the headers and docs are for your convenience, and do not get saved anywhere in the frameworks or your app when compiled. Adding a new delegate method just means adding code like above. You can't ask the table view, "do you use this method?" because there is no universal way to query this.

"Delegate methods" are completely outside the language; they're a construct which uses language features but has no actual meaning within the language. When you do     [obj respondsToSelector:...], you're asking if it responds to that selector, and if it implements it then it always will; delegates have no bearing on whether this will reply with YES or NO.

