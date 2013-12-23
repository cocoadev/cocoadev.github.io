---
layout: page
title: ApplicationPreferences
---

Whenever my Cocoa app wants to access Application Preferences it will crash with a Bad Access Signal.  I use the NSUserDefaults and store my preferences in it just fine.  But when one of the Cocoa Frameworks wants to store its preferences in the application preferences ( such as the recent directories for the NSSavePanel, or the NSSavePanel preference alwaysShowExtension ) the app will crash.  This is not my first cocoa app, however, it is the first multithreaded app in cocoa. ( I'm an experienced c++ developer in the pthreads environment. )  I am thinking that it has to do with the NSAutoreleasePool since the last call on the stack is always objc_msgSend preceded by a CFRetain. Then further up the stack will be the calls by the framework like, CFApplicationPreferencesCreateValueForKey.  The second thread has its own NSAutoreleasePool.  These dialogs are being opened by the main thread.  Some miscellaneous facts about this app:  Its using OpenGL (mainly in the second thread), parts of it have C++ mixed in (.mm), some parts are exclusively C++ and have a .cpp extension. It has multiple windows and stores preferences. 

FYI on Jaguar it doesn't even run. Jaguar wants to store the window location when  the window first gets focus. ( So if I run it in the debugger and set a break point before the window is shown, it will run ( because the window is created behind the project builder window and when I move the pb window out of the way, I can see my main window rendering the OpenGL sequence ), so long as a don't click on the window.  When I do it crashes trying to save the window size/location in the application preferences. 

Any ideas? 

-- Mike Harris

----

Sounds to me like maybe you're releasing your NSUserDefaults object when you're done setting a specific value. That's a definite no-no, since the returned is assumed to be autoreleased, but might be cached by the implementation. 

But without a sample project to work from, this is a total shot in the dark.

-- MikeTrent

----

I'm not saving the pointer returned by [ NSUserDefaults standardUserDefaults ] since I was under the impression that each time it is called, you get a pointer to the shared instance for the application back.  I've been using it like this to set the application defaults:

    
 [ [ NSUserDefaults standardUserDefaults ] registerDefaults: defaults ];

where defaults is a NSMutableDictionary*.  

Then to changing preferences like this:
    
 [ [ NSUserDefaults standardUserDefaults ] setObject: [ NSNumber numberWithInt: aNum ] forKey: kFramesPerSecond ];


And then retrieving preferences like this:
    
 NSNumber* theNum = [ [ NSUserDefaults standardUserDefaults ] objectForKey: kFramesPerSecond ];


I'll try saving the pointer and retaining it.  Does this pointer need to be retained for the life of the application? Thanks.

( Update: This change didn't make an improvement. )

-- Mike Harris
----
For me it's not very clear what are you exactly doing as well. Are you reading/writing preferences from both threads? If not, it's not important that your app is multithreaded. Check if all pointers are valid... or post the code somewhere.

-BobC
----
NSUserDefaults objects have a     setInteger:forKey: instance method. Try that instead.

----

I'm afraid I may have confused things. When I say "since the returned [object] is assumed to be autoreleased, but might be cached by the implementation." that means don't do anythng special with the returned object. Do not save the pointer and retain it. Your preference handling code above looks correct to me, assuming your objects (like the "defaults" dictionary) aren't being over-released. 

Do you register your defaults on the primary thread? Are you always crashing on the primary thread? Does the app crash when defaults are read, or when they're written to? Does your app crash if YOU write to your defaults? Or just when the other guy does? Specifically how is this "defaults" NSMutableDictionary created?

If this is still giving you grief, feel free to send me a backtrace in e-mail and we'll see if we can work this out.

-- MikeTrent

