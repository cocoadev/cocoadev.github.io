---
layout: page
title: ReleaseCocoaVariableAllocedInStaticFunction
---

 

Hi,all

I am writing some code relating to the accessibility issues and need to call AXObserverCreate() API,so the second parameter should be a callback whick function head should be as below:

static void callback (AXObserverRef observer, AXUIElementRef element, CFStringRef notification, void *refcon)

I think this callback function can not be implemte as a method of o-c class as its function head can not be changed.But when I try to alloc a NSSpeechSythesize in the callback funtion and using it..I send release to the the NSSpeechSythesize variable to free the memory.But sadly the application didn't actually free the memory.After the application involked the callback function for 30 or 40 times,the application's memory usage jump to 250~300Mb.If I don't alloc any cocoa variable in the static function,the app runs OK!

I try to add the NSAutoreleasePool around the alloc and release period.But it doesn't work also..

I am confused.Is there any way for me to do this thing or some workaround for the issue?

Thanks for your hints!

----

PostYourCode

