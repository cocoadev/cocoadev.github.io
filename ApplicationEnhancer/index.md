---
layout: page
title: ApplicationEnhancer
---

Application Enhancer (APE) is a system by UnSanity allowing developers to create Application Enhancer Modules, that would extend the way applications work. Most of UnsanityHaxie products are created using the technology behind APE, and being migrated to APE module format as of time of this writing (mid-August 2002).

The SDK is available for licensing now, and provides facilities for loading your code into any application's context, patching (including objc methods), and messaging between patched processes.

Application Enhancer is out. Drop us a line at mailto:sales@unsanity.com for more information.

You can download the APE SDK from http://www.unsanity.com/haxies/ape/

----

Why not post some more information here?  I wonder how it works.  (From the user perspective as well as the developer perspective.)

----

Here's an example.  When Finder displays an icon of a file it calls the Carbon API     GetIconRefFromFileInfo() to get the icon.  Now if I want to add an overlay on top of this (say to mimic Labels as per OS 9) then I would want to intercept any calls to this function and do my own processing.  Here's the code to do that:
    
 APEPatchCreate(&GetIconRefFromFileInfo, &My_GetIconRefFromFileInfo);

Here APEPatchCreate is one of the functions exposed by the APE sdk.

When you install Application Enhancer it puts a deamon that monitors application launches.  When an application is launched it searches     Library/Application Enhancers for any APE's to be loaded.  So say Finder was being launched, then Application Enhancer would find our APE and load it into Finder's address space.  It would then call our initialization routine where we would go on to call APEPatchCreate().  

--SaileshAgrawal

----

Is there any way to block this for a specific app whether or not Application Enhancer wants me to? Three times in the last few years, users with Application Enhancer installed (I don't know what APEs they were using) had very odd problems with method calls failing with errors that make no sense for the arguments passed to them (I know, because I've sent a debug version to each of these users). In each case, it took me days to narrow it down to third-party interference. In EVERY CASE, removing Application Enhancer solved the problem. I know I'm opening myself up to a flame war because any time a developer blames Application Enhancer, its creators come running and deny any possible blame. If that's about to happen this time, save it, please, I'm not interested. All I'm interested in here is how I can prevent Application Enhancer from touching my app.

----
I don't know of any way to *block* APE from touching your application, but you could look for the aped process and ask the user to remove APE. Be warned -- the Haxies are there because the *user* wants them. If your program complained about my choice, I'd delete it in an instant.

----
I suggest taking a different approach. Rather than blocking APE, when a user writes in with a problem, as part of your initial troubleshooting, ask them if they have APE installed, and tell them to add your app to APE's master exclude list if so. If the problem goes away, then you can tell them that it's caused by an APE module and that they should go through to figure out which one.

Blocking APE from your app is not only rude, but also rather unnecessary. If you've only had this happen three times in the last few years, then either your app is spectacularly unpopular, or APE doesn't cause problems with your apps in the vast majority of cases. Blocking APE completely just to prevent something that causes some pain once a year is basically destroying the village in order to save it. -- PrimeOperator

----

Rude?? I don't agree with that, but I can say that the app in question sells a minimum of 4 units a day. It success or lack thereof has nothing to do with this conversation, so why bother saying it unless you're trying to start a flamewar? Yet every time this problem has appeared, removing/disabling ApplicationEnhancer has resolved the issue. As to haxies being there because the user wants them to be, I'm sure the user wouldn't want them there if the user was fully aware of how many odd problems APEs can cause. In fact, every time we've determined this as the problem, the user removed ApplicationEnhancer without a moment's thought (which demonstrates the priority of a user trying to remain productive). I **do not** want this to become yet another religious war over ApplicationEnhancer. If you don't have an answer to my question, fine. If you want to get into an APE holy war, keep your comments to yourself.

----
I mentioned "spectacularly unpopular" as a joking alternative. Notice I mentioned two things up there, connected with "or"? My point was this: you're selling a minimum of 4 units a day. Unsanity's stuff is installed all over the place. That means that there are a lot of your users with APE installed, and yet you've only had this happen three times. Do you seriously believe that only three of your users have ever had APE? That is not likely. What is more likely is that APE doesn't cause problems most of the time.

It is rude to your users to block APE unless you are certain that it causes issues a large part of the time. They installed it by choice, and if it only causes rare problems (which sounds like it's the case), then preemptively disabling something they installed is not very nice.

Also keep in mind the difference between APE and APE modules. APE itself basically never causes problems, but APE modules sometimes do. While it appears that you know this, I think it bears repeating, and many people don't understand this.

In any case, do what you wish. If you believe that a rare problem is enough justification do block APE from loading into your app, go for it. However, I'd suggest that posting here is not the right way to find out how to do it. Instead, read through the APE SDK if you haven't already, and if that doesn't turn up anything, e-mail Unsanity.

Also please note that I'm trying my utmost to be helpful here. I realize that I'm not rolling over and answering your question, but neither am I trying to criticize, but rather offer helpful alternatives. If you don't like that, fine, but please realize that I'm not trying to flame. -- PrimeOperator

