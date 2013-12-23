---
layout: page
title: DeInitialize
---



I have a few classes in my code which contain only static functions.
Such as:

+ (void)doSomething;
+ (void)doSomethingElse;

They are programmed this way because they're central classes, and almost every other class in the app accesses them.  There are several variables within these classes that must get initialized, so the +(void)initialize method takes care of it.  I realize that when the application exits, I don't have to specifically release my static objects because the OS will be re-posessing my memory.  HOWEVER, what about the following code:

+ (void)initialize
{
    // do some stuff here
    IORegisterForSystemPower( refCon, &notifyPortRef, MySleepCallBack, &notifierObject );
}

I'm not entirely sure if I NEED to call IODeregisterForSystemPower before my application exists, but I thought I probably should just in case.  (And if you know the answer to this question, I'd also love to hear it.)
Where would be a good place to do this?
I know that init has a corresponding dealloc function.
Does initialize have a corresponding deinitialize, or something similar?
Is there a better way?  Or should I just create a cleanup function in all my central classes, and specifically call all of them prior to exiting the application?

----
----

*"They are programmed this way because they're central classes ..."*

----

You're referring to "class methods" as opposed to "instance methods". Using the correct terminology will go a long way. :-)

There's also nothing "static" about the class methods.  They are fully polymorphic unlike static member functions in C++.
Now, quite apart from whether the original poster's design pattern makes any sense at all in Objective-C, the answer is to register for the 
applicationShouldTerminate: or applicationWillTerminate: notification or delegate message and do any clean-up there. 

http://developer.apple.com/documentation/Cocoa/Conceptual/AppArchitecture/Tasks/GracefulAppTermination.html

----

Yes, "class methods" is what I meant.  Thanks for reminding me.  It gets confusing when you have to switch back and forth between Java at work, and Cocoa at home.

And the notification NSApplicationWillTerminateNotification' will work just perfectly.  Thank you.

