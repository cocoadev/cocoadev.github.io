---
layout: page
title: CreateaBasicDockling
---



Ok, i'm new to Cocoa, but I have experience with C++ and Jav. I can't find any information anywhere on how to create a stand alone dockling that can be used to launch apps. I just want the dockling to save multiple alias's of applications that can be launched from the dockling. Anyone have any ideas? I thought this would be a relatively simple project to work on to help me grasp the concepts of cocoa and OS X, but it is proving to be more difficult than expected. 

----

As I understand it, docklings are deprecated by Apple. -- KritTer

----

Here's a good tutorial for making docklings:
http://www.stepwise.com/Articles/Technical/2001-03-30.01.html

Docklings are deprecated, but that doesn't stop people from using them. They are much better than making a small application with a dock menu IMO.

Hope that helps

----

Be aware of the pitfalls in using a deprecated, private framework - changes by Apple are probable, and will almost surely break your code. If it's just a project to learn Cocoa, this may not matter to you -- KritTer

----

They're also not necessarily superior; docklings run in the Dock's address space, IIRC, which means that if they crash, the Dock goes with them. Not so good.

-- RobRix

----

This is true, but also remember that most docklings are quite lightweight, and rarely crash. Besides, programs should be coded well in the first place :)

----

I dislike relying on the coding skills of others ;) Seriously, though, I guess it's a matter of what sort of load you're going to have. More complex things would probably be better off as applications, I suppose.

-- RobRix

----

I am pretty sure docklings run in the adress space of the SystemUIServer (called DocklingServer in earlier OS versions). That means that when they crash they take other docklings and menulings with them, but everything else survives. SystemUI will then be restarted by the dock.

-- DavidRemahl

----

Why are they depreciated? What's their replacement?

I have a Network Statistics dockling in my dock. How does one make something similar without docklings? Obviously a separate app with a dock menu and a changing tile would work but a dockling just seems so much more lightweight.

DavidRemahl's right about the SystemUIServer thing BTW.

-- PeterMonty

----

Well, afaik, using applications with dynamic icons is the only way that apple supports in any way.

-- DavidRemahl

----

Apple suggests creating a very small application with a dock menu and a dynamic dock icon. This is better because it does not run in the docks space, but it has a few disadvantages. One of them is that it is an application, so when command tabbing, you may bring it to the front by accident. You also must have it's icon on the applications side of the dock separator. My biggest problem with it though is that you have to either control-click, hold-click or right-click to bring the menu up, if you don't it'll just come to the front. Any click on a dockling will bring up the menu.

I just hope Apple gives developers a better way to do dockling like things.

-- Kent Sutherland

----

Yes, I appreciate docklings as well. (http://www.ittpoi.com/clockling.html - but be aware that it doesn't run under 10.1). I used to put them in the doc part of the dock, but that possibility is now gone :(.

-- DavidRemahl

