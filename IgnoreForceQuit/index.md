---
layout: page
title: IgnoreForceQuit
---

I'm try to develop an app to lock my computer and log the password attempts and other such coolness. I've gotten it working except for one glaring issue: You can Command-Option-Esc to force quit it fairly easily. Is there anyway to bypass the force quit keys so my application will not quit?

----

**Do you grab fullscreen etc? I think that would at least keep the force quit dialog from coming to the fore... If that isn't satisfying enough, probably there's a POSIX way to handle (i.e. ignore) the SIGINT or whatever it is you receive when the user force quits you.**

----

Actually, I was able to figure it out. I used the CGCaptureAllDisplays() to get my app above everything else (there's a great article on Cocoa Dev Central [http://www.cocoadevcentral.com/] for this topic). The problem isn't that Force Quit window would come up, but the application would just quit itself instead. It was getting a SIGINT, but I'm not familiar enough with the POSIX functions anyway. So, I hunted around a bit and found the SetSystemUIMode() function in the Carbon framework. Here's the article on ADC about it (which is also a very good article): http://developer.apple.com/technotes/tn2002/tn2062.html

----

Actually, I do not think there is a POSIX way of doing this. If I am not mistaken, Force Quit sends a SIGKILL which cannot be caught using the signal() function. The manpage for signal says the following: Except for the SIGKILL and SIGSTOP signals, the signal() function allows for a signal to be caught, to be ignored, or to generate an interrupt.  These signals are defined in the file <signal.h>

Martin Mroz

----

So the point is not to find a way to ignore, SIGKILL, it's to prevent the user from issuing SIGKILL in the first place.  Disabling the force quit dialog and such.

----

Long time lurker, first time poster, new to OS X, but long time Solaris, AIX, *BSD, and Linux admin/user.  ** If ** OS X allows you do do this (via WindowServer, KernelEventAgent, or whatever) ** don't **.  What if a different program needs to be killed?  And if done via WindowServer, or whatever mechanism is used to disable the Force Quit dialog, you've done nothing to prevent * kill -9 <PID> * .

----

Welcome!  However, we're not talking about normal situations.  Click on the tech note link above (which solves the problem, btw), and consider kiosk applications.

