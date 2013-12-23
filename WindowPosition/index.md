---
layout: page
title: WindowPosition
---

A window has a **frame** (an General/NSRect data structure obtained by something like the following:

    General/NSRect myWinFrame = [ myWindow frame ];

The frame is always reckoned in screen coordinates, according to easily-accessible documentation.

An General/NSRect structure has the following     float components:

    theRect.origin and     theRect.size    (if you don't understand the dot notation consult a C reference such as K &R)

the     origin is the x-y position of the General/NSRect (the lower left-most corner, to be precise)
This in itself is an General/NSPoint data structure, and you can refer to the     float component variables, e.g., by     aPoint.x and     aPoint.y
assuming an General/NSPoint variable declared as     aPoint.

The     size is the x-y *dimensions* of the General/NSRect
This is an General/NSSize data structure, with     float components referred to as     aRect.width and     aRect.height

The worst it will ever get is if you want to refer to one of those subcomponents via the General/NSRect variable itself, to wit:

    myRect.origin.x or     myRect.size.height

----

Examples:
    
General/NSPoint myWinOrgin = [ myWin frame].origin;
General/NSSize myWinSize = [ myWin frame ].size;
float myWinLowerLeftX = myWinOrigin.x;


If you have trouble obtaining such data from one of your windows, make sure the window variable actually points to a window object.
This could happen, for example, if you have not connected a window outlet in General/InterfaceBuilder, or perhaps your window initialization
failed. But those are subjects for A**'notherPage.
If myWindow is nil, then [myWindow frame].origin would cause a crash.

----

Here is something slick you can try with window positioning:

Change the origin to give the illusion of an item flying across the screen as it were with -setFrame:display:animate: 

    if(aWindow) {
General/NSRect newRect = [aWindow frame];
newRect.origin.y -= 30;
General/NSLog(@"%.0f",newRect.origin.y);//Remember this is a float to log, but there is something there.
[aWindow setFrame:newRect display:YES animate:YES];
}

Uses CPU while running, but there is no niftier sight :-).

----

The window resizing animation effect seen, for example in System Preferences is discussed further in General/WhereIsCoolSystemPrefsWindowEffect

For more details see General/AutoWindowResizing General/UFISpringyPanel
