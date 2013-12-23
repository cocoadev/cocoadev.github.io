---
layout: page
title: AppleScriptSupport
---

I have posted the source for an extremely simple AppleScript enabled Cocoa app on my site.  Interested parties can download it and read about it here:

http://www.scifihifi.com/weblog/mac/AppleScriptDummies.html -link is not working anymore

There are a number of Cocoa AppleScript support examples on the web, but I think mine is useful because:


* It is pretty much the least complicated example possible.
* It demonstrates the NSApplication scripting category pattern necessary to allow your application itself (as opposed to, say, an NSDocument subclass) to respond to AppleScript commands and properties.
* It contains annotated Script Suite and Script Terminology files that I think help demystify the script modeling process.


I hope people find it useful!

-- BuzzAndersen

----

Update: Since creating that example, I learned that it is not necessary to use the NSApplication category anymore, since NSApplication now has a delegate method called "application:delegateHandlesKey:".  Simply return YES from that method for key x, and NSApplication will pass calls to x and setX on to your delegate.  Thought everyone would like to know...

----

If you're tinkering with Buzz's example to add delegateHandlesKey, be sure to comment out the call to "NSLog([[NSApp theObject] title]);" in the delegate's applicationDidFinishLaunching method. Otherwise you'll wonder why on launch, NSApplication still complains about not recognizing a selector called theObject (since that call doesn't go through key value coding, it doesn't get redirected to the delegate).

----

Unfortunately Buzz's example doesn't deal with one of the most problematic issues, which is supporting object descriptors. For example, I'd like to see how to support scripts like this:

    
tell application "ASTest"
	-- this assumes that the app is modified to have N testObject elements
	set tempObj to the first testObject
	get the title of tempObj
end tell


Supporting this would require 'SimpleObject' to have an -objectDescriptor method. However, I have not been able to get such a setup working. With the debug output on, I get "Error converting apple event to script command: -1700".

