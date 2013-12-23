---
layout: page
title: TerminateOrStopGood
---

If like me, you have a time limited demo app that you want terminate programmatically, then you'll probably have discovered that General/NSApplication's     terminate: is often ineffective, particularly when a modal dialog or sheet is running. In fact, anyone in the responder chain only has to return NO to the     applicationShouldTerminate: message to cancel the process.

There's not much else in Cocoa to help you with the task, except the promising sounding     stop: which is also an General/NSApplication instance method. Unfortunately     stop: is also foiled by modal sessions. However, while it doesn't quit the app if a modal session is active, it does stop the current modal General/RunLoop.

How do you ensure that your app will close when you want it to then? The best solution if you really want your application dead, is to bypass Cocoa entirely and use the faithful ol' Standard C library:

    
exit(EXIT_SUCCESS);


This is a mighty brutal way to kill an app, and things won't be freed cleanly. However, as long as you don't have file/network writing threads or similar, then your app is dead and it really doesn't matter!

If you really want to stay in the Cocoa world, you can experiment with these methods. They have their own gotchas though, so be careful.

    
General/[NSApp stop:self];
if(General/NSApp && General/[NSApp isRunning])
  General/[NSApp stop:self];
if(General/NSApp && General/[NSApp isRunning])
  General/[NSApp stop:self];


If you have methods which are called during termination (like windowWillClose), you may want to wrap any calls in the following, since General/NSApp and friends may not be in a great state of affairs after the bludgeoning:

    
if(General/NSApp && General/[NSApp isRunning])
{
  //termination code like General/[NSApp terminate:self]
}


-- General/HeathRaftery
