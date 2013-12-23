---
layout: page
title: FindOutWhichScreenDockIsOn
---

I'd love to find out which screen the Dock is on. How would I go about that?

----
The Dock is always on the main screen, which can be found using     [[NSScreen screens] objectAtIndex:0]. Why do you need this?

----

Hm, I'm the developer of HierarchicalDock (www.eternalstorms.at/utilities/hierdock) and a user said the hierarchical menu would open on one screen, although the Dock was on the other, and my menu always opens on the mainScreen...
And I guess one could also use     [NSScreen mainScreen], right? It's the same as ...objectAtIndex:0, isn't it?

----
No it is not. RTFM.

----
Whoa, OK, big misunderstanding here on my side.
mainScreen: the screen with the key window.
objectAtIndex:0 : screen with menu bar.

Thanky.

----
I don't think that's correct - if I move the Dock between the left and the right then it draws on different screens but     [[NSScreen screens] objectAtIndex:0] doesn't change.

----
Good point there. I don't use the side dock and didn't realize it would hop onto a different screen if you did so.

In that case I don't see a direct way to discover which screen it's on. An indirect way could be to iterate through the list of screens and find any where the frame and visibleFrame aren't equal. Screen 0 will always be unequal due to the menu bar. If another screen is unequal, the Dock is there. If all the other screens are equal, then the Dock is on screen 0.

----
What about if the Dock is auto-hidden? It wouldn't work, because the frame and visibleFrame would then be the same, right?

Update: Well, turns out that's exactly what happens. When the dock is hidden, my visibleFrame.height is about 980. When the dock is not hidden, it's 937 or something.
Any other suggestions?

Yet another update: The documentation says even if the dock is hidden, the visible frame will be a little smaller than the screen size, because mac os x has a tracking area to find out when to show the dock. So, based on this, my previous poster was right. And here's how to do it:

    
BOOL dockIsHorizontal = YES; //Dock is on top or bottom, if no, it's left or right - you can find that out with the dock's preferences file
for (NSScreen *curScr in [NSScreen screens])
{
	if (dockIsHorizontal)
	{
		float toSubtr = 0;
		if ([NSMenu menuBarVisible])
		{
			toSubtr = [[NSApp mainMenu] menuBarHeight];
		}
		float heightWithoutMenBar = [curScr frame].size.height - toSubtr;
		if ([curScr visibleFrame].size.height < heightWithoutMenBar)
		{
			//the Dock is in this screen
		}
	} else
	{
		if ([curScr visibleFrame].size.width < [curScr frame].size.width)
		{
			//Dock is in this screen
		}
	}
}

