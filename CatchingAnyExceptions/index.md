---
layout: page
title: CatchingAnyExceptions
---

How would I go about catching ALL uncaught NSException's? Apps like Adium seem to catch any exception that occurs (and then launch a helper app). I desperately need something like this... anyone know how to do this? --KevinWojniak

Ok I found out how....

There is an ExceptingHandling.frameworking found in /System/Library/Frameworks which allows you to catch uncaught exceptions in your app. Here's what I used:

    
NSExceptionHandler *handler = [NSExceptionHandler defaultExceptionHandler];
[handler setExceptionHandlingMask:NSLogAndHandleEveryExceptionMask];
[handler setDelegate:self];


and then this is called on the delegate:

    
- (BOOL)exceptionHandler:(NSExceptionHandler *)sender
     shouldHandleException:(NSException *)exception mask:(unsigned int)aMask
{
	/* handle the exception */
}


----

I've heard this doesn't work, because no exception is ever unhandled -- NSApplication installs an any-exception handler of its own.

----

Well you heard wrong ;). I implemented it and it does work. It allows you to override NSApplication's default way of handling exceptions (NSLoging it and terminating).

