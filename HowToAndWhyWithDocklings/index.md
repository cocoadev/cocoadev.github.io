---
layout: page
title: HowToAndWhyWithDocklings
---

HowToAndWhyWithDocklings - Trying to make a good resource for developing docklings... unless anyone know of such a place already?


----
First up is me... How do I dynamically change the icon of a dockling? I know how to do it in an Application.

-- Frode Danielsen

----

How exactly are you implementing your dockling? Ye olde dockling API of 10.0.x or NSDockExtra (if that even works, not sure).

 -- FinlayDobbie

----

I've only tried the old dockling API. Didn't know of any NSDockExtra. Any hints/tips/pointers etc. are very welcome! :-)

- Frode Danielsen

----

Ok. After a lot of trial and error I figured a way of doing this. Thnx to someones EightBall dockling. Here's the code needed, using the old Dockling API:
    
- (void)changeImage:(NSImage *)newImage
{
    NSImageView *iconView = [[NSImageView alloc]
            initWithFrame:NSMakeRect(0.0, 0.0, 128.0, 128.0)];

    [iconView setImage: newImage];
     
    self window] setContentView:iconView];
    [iconView release];
    [[self window] setBackgroundColor:[[[NSColor clearColor]];
    self window] display];
}

-Frode Danielsen

----

Developing docklings with the old API leaves you no way of loading it inside Project Builder to view debugging information etc. Still, there is a way to become a bit more enlighted when errors occur. Use the standard [[NSLog function for printing out info as your docklet runs, to view these log messages, open the Console.app in /Applications/Utilities/. Very handy indeed :-)

-- Frode Danielsen

----
Has anybody had any success with putting pictures into dockling menus? I haven't :(

----

It doesn't work. Sorry :(

-- KentSutherland

