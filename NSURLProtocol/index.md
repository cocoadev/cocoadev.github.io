---
layout: page
title: NSURLProtocol
---



I'm interested in writing my own protocol in Cocoa.  I've had a look at the NSURLProtocol class and understand that I have to subclass this.  Does anybody know of any good tutorials on this?

-Chris

----

Hi everybody,

I want  to handle custom type urls in my app. I'm registering CFBundleURLTypes in app's Info.plist and also I'm subclassing NSURLProtocol (and register it with     [NSURLProtocol registerClass:[MyURLProtocol class]]). The problem is that when I click on myapp://link the app is launched but no one method of NSURLProtocol is called. What am I doing wrong?

----
I believe that interaction takes place through AppleScript. I don't use anything related to NSURLProtocol.
Here's how it works for me:

My scriptTerminology file contains the following:

<code>
"GetURL" =
                {
            CommandClass = someclass;
            AppleEventCode = GURL;
            AppleEventClassCode = GURL;
         };
</code>

where someclass contains:

<code>
- (id) performDefaultImplementation 
{
       NSLog(@"Called as URL handler");
}
</code>

Good luck,
Cristi
----

You want to do something like this: [[NSAppleEventManager sharedAppleEventManager] setEventHandler:self andSelector:@selector( handleURLEvent:withReplyEvent: ) forEventClass:kInternetEventClass andEventID:kAEGetURL];

HowToRegisterURLHandler

-- will

