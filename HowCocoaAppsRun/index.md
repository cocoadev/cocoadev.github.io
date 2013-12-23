---
layout: page
title: HowCocoaAppsRun
---



Hey guys... I was wondering if anyone could point me in the right direction.  I've been looking around a little but I dont know the right termonology to look for.

I always tend to grasp things better if I can see how they actually work, rather than just following directions or tutorials and taking the behind the scenes stuff for granted.  So I'm trying to look at a few Cocoa apps and figure out exactly what is going on from start to finish.  Every cocoa project I've built from a template seems to begin with:

    
#import <Cocoa/Cocoa.h>

int main(int argc, char *argv[])
{
    return NSApplicationMain(argc,  (const char **) argv);
}


I can see its importing the Cocoa header, and I know main() is called when the app starts and returns the result of NSApplicationMain.  A little searching showed me this on some website...

*
The NSApplicationMain function creates the application, loads the main nib file from the application's main bundle, and runs the application. You typically only call this function once, from your application's main function, which is usually generated automatically.
*

I know very little at this point about bundles or nibs inner workings.  For my purposes I'll assume that the main bundle is the application bundle, but what actually tells the app which is the main nib file?  Is it in a .plist somewhere?

Also, what should i look up for information on what happens when the main nib is opened.  I'm guessing that it goes through a series of events, including AwakeFromNib but I'd like some good info on the actual process.  I've been on & off with Cocoa for a while and have made some headway but would like to start clearing up some of the fog.

Thanks!
CliffPruitt

----

*You set this up in the Info.plist file (the easiest way is by inspecting your project's target). There's an entry that automatically designates "MainMenu.nib" as the ... well ... main nib. You can check the following link for a good starting point for the second part of your question:* file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/LoadingResources/index.html *... you also might want to peruse the XCode documentation as the answer to your first question is covered there as well. You must read the instructions on how to use your tool if you're to use it effectively!*

----

**It sounds like you may be interested to read about the RunLoop.**

----

Thanks for the info.  It is a good starting point.  I'm partially familiar with the run loop, but I could definately stand to clean up on that subject.

With regard to "You must read the instructions on how to use your tool if you're to use it effectively!" I couldn't agree more.  I am actually very big on reading documentation, however if I read all the docs before asking questions I really dont think I'd get anywhere very fast. :-)  (Kinda like taking a math class & a teacher telling you to hold your questions till the end of the semester)  I'm still having something of a hard time with finding my way around the Apple documentation so looking for a specific answer is sometimes a little reough, especially when I dont know exactly how to phrase the question or the term to look up.  I usually need to explore something hands on alongside the documentation for it to make any sense to me, so I hope the occasional newbie question isn't too much of an inconveniance.  SO thanks a lot for the feedback & the info.

Thanks
CliffPruitt

'Not at all - it sometimes helps, though, to get a few keywords and be reassured that the information is there (and where 'there') is. The XCode help will steer you in the right direction there.*

**That's great, but ... why are you replying to yourself?**

----

