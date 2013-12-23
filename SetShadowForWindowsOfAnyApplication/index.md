---
layout: page
title: SetShadowForWindowsOfAnyApplication
---

As described in CoreGraphicsPrivate, I tried to modify the shadow bit for windows of any application.
I use the following code for it:
    
CGSConnectionID cid = _CGSDefaultConnection();
NSPoint curP = [NSEvent mouseLocation];
CGPoint myPoint = CGPointMake(curP.x, 1024-curP.y);
CGPoint outP;
int widOut;
int cidOut;
CGSFindWindowByGeometry(cid, 0, 1, 0, &myPoint, &outP, &widOut, &cidOut);

CGSWindowTag tags[2] = {0,0};
tags[0] = CGSTagNoShadow;
CGSSetWindowTags(cidOut, widOut, tags, 32);

What this does is get the cgsconnection and the windowNumber of the window the mouse Pointer is over, and use that for CGSSetWindowTags to set the noShadow bit.
Now, it works for windows in my application where this code is executed in, but it doesn't work for other applications I move the mouse over.

Is there anything I'm missing or is it just not possible?

----

Why, why, why? Intellectual curiosity or a desire to make an application/utility that has no merit whatsoever?

----
It's just curiosity. That and that somebody asked me to do it. So why not, if they need it?

----
Arguments of "bad idea" aside, it's generally not "possible within the boundaries of the API" to do things like this to other applications. It *is* possible, however, if you use third-party solutions like the highly-controversial Application Extender ...
----
Ok, the AE is something I don't want to burn my fingers on, so I guess that's it, then. Thanks for the info. Nice to get an actual answer ;)

----

*"... burn my fingers on ..."* 

Nor your users ... The authors defend it staunchly, and are generally a very intelligent bunch with persuasive arguments. Unfortunately, the fact remains that not only are the *modules* typically not that solid (not APE's fault, but the modules') but APE itself has issues, evidenced by the recent Leopard upgrade fiasco, where they *insisted* it couldn't be APE because APE is designed not to run on newer OS versions ... Yeah, it was APE. ;-) Blah. It's still the equivalent of installing after-market "booster" parts to your car. You're trusting your investment (the car and its viability) to manufacturers who didn't design the car and are forcing it to do something it wasn't designed to do. Are you willing to risk it? I'm not.

----
Of course, unlike a car's booster parts, these parts can be removed in seconds if you find out that they're causing trouble. The only real trick is that they sometimes cause trouble but don't make the source of the trouble obvious.
----
I read somewhere around here on cocoadev that things like this have to be run as root. Now generally, running anything as root is a bad idea and no worries, I'm not going to release anything like that. I'm just interested in if it would work would I run the code above as root?

----
Many CoreGraphics functions are protected (by the window server) : you can use them in your application on your windows. But you cannot use them on other windows, because you don't have permission.
Running as root doesn't give this permission.
To get this permission, you must run as "universal owner" : this is a process which is authorized to do all.
Currently, the universal owner is the Dock, and it cannot be changed "easyly".
One tip to get this permission is "code injection" : this is to inject your own code into the running Dock process. Then, this code will act as "universal owner". By implementing a communication system between your application and your injected code, you'll do call all CoreGraphics functions successfully.

MrBru.
----
In the private CoreDock API (see DockPrefsPrivate) there's something called CoreDockRegisterAsDockOwner or something like that. Could that give me those privileges?

----
Now that we have a forum for this kind of question-and-answer, please migrate this discussion there, and if/when you receive a satisfactory answer, edit this page as appropriate.

