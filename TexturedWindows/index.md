---
layout: page
title: TexturedWindows
---



Is there any way to programmatically set window texture?

I want to be able to have a preference as if the main window is textured, similar to using Safari Enhancer to change if Safari is textured.

----

Two separate nibs for textured and non-textured? Or you could create the windows programmatically with the General/NSTexturedBackgroundWindowMask of the style mask set.

----

I've seen a VNC server do just that, and it didn't have two separate nibs.

----

A trick that may or may not work is calling -init... on an existing object. I.e.     [myWindow initWith...] where myWindow was already -init'ed. Worth a shot!

-- General/RobRix

----

Call this on the fly to change window texture on and off: General/[NSWindow _setTexturedBackground:(BOOL)toTextured]; It is hidden API and can be changed later.

-- General/HaoLi

----

It should be noted that when using _setTexturedBackground: to change the texture, the drop shadow is not re calculated. This causes the shadow to stay square instead of rounding when changing to textured, and vice versa. Therefore, you should either have it change to textured next time the app is launched, or have it recompute the drop shadow when you set the texture.

-- General/JacobHazelgrove

It seems like [windowName invalidateShadow]; should work for that

----

It would also be prudent to check if the object responds to the selector. As this is an undocumented API and may go away with future versions of the framework.

BOOL canWeChangeTextureOnTheFly = General/self window] respondsToSelector:@selector(_setTexturedBackground:)];
if (!canWeChangeTextureOnTheFly) return;
// code goes here

Enjoy

-- [[HasanDiwan

----

It has issues redrawing toolbars and when clicking buttons which change the texture it creates funky quartz effects. Probably why it hasn't been released yet. *awaits Tiger for this one* -- General/MatPeterson

----

I have been working on an app that draws some custom controls, and based on the type of window (aqua or textured) they need to draw differently. I was wondering if anyone knows how to find out. I have tried calling styleMask, but it gives me an int with a value of 7 for an aqua window and a value of 263 for a texture window, the problem is that the value will change based on other values (such as if a window is resizable or not). Any help would be greatly appreciated. 

----

Try doing something like
    
if (([window styleMask] & General/NSTexturedBackgroundWindowMask) != 0) {
    // I am clean, sharp, nice-looking metal
}


----
Hello. I'd like to make a textured window with the texture of an app like iTunes and Democracy. I've seen the Democracy source code and it is confusing (plus, I don't know Python). So how do I do that? - General/PietroGagliardi

----
This article explains how:

http://www.rogueamoeba.com/utm/posts/Article/polished-metal-2005-09-10-10-00.html
