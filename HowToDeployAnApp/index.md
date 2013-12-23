---
layout: page
title: HowToDeployAnApp
---



I am a newbie Cocoa programmer. I created a small application on XCode 2.1 on my computer running OS 10.4, and it works fine. When I move the built application to another computer in the lab, which is running 10.3, it does not work. The crash log reveals that it is trying to find the zerolink framework, even though I built it under deployment mode with zerolink supposedly turned off. Also, no matter what target OS SDK I build for (i.e. if I use the compiler default 10.4 or if I change it to 10.3) the same problem occurs. Is there something obvious I am missing? 

----

You project is probably not copying a framework you're making use of into its bundle. I don't think there's a "Zerolink Framework", it's more that it can't find the framework your code is referencing. Does your project use a Framework that you needed to add, or is it just the standard system frameworks it uses. The exact error would also be helpful. -Seb

----

Make sure you do a clean before rebuilding in deployment mode.

----

Any time you're posting about a problem and you start talking about a log, that's when a little voice inside your head should be saying, "hey, you ought to *post* that log so others can see what's going on too!" If you can show the relevant bits of that crash log, no doubt we can give you more info.

----

You're quite right I should have done that in the first place. Here's the crash.log:

Command: MyApplication
Path:    /Users/*****/MyApplication.app/Contents/MacOS/MyApplication
Version: ??? (???)
PID:     14313
Thread:  Unknown

Link (dyld) error:

dyld: /Users/*****/MyApplication.app/Contents/MacOS/MyApplication can't open library: /System/Library/PrivateFrameworks/ZeroLink.framework/Versions/A/ZeroLink  (No such file or directory, errno = 2)

This particular file exists on my computer but not on the target computer. I tried dragging it across, but I don't have write priviliges there, and that shouldn't be the real solution anyway. Again, I am cleaning all targets before rebuilding in deployment mode, and the Info window does show that  zerolink is turned off. Thanks for any insights.

----

You're using ZeroLink. There's more than one place to turn it off, evidently you just didn't hit the right place yet. More specifically, you have to disable it in your BuildStyle/BuildConfiguration, not just the main target.

