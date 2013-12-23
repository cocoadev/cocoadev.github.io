---
layout: page
title: DrawerBorders
---

I'm developing a children's application and all the windows are relatively odd shaped.  The problem I'm having is with the border around drawers.  Unfortunately, I do require the drawers.  I'm able to remove the opacity from the drawer's content view and give it a clear color.  However, I have yet to figure out how to get rid of that border.  I was wondering if anyone has had any luck with this.

----

There *is* no way to do this. The portion of code that draws the borders for the drawers is private. That is, it's not accessible to developers. You could use class dump to figure out which *private* methods are responsible for this, then override them and do what you want, but I would never recommend shipping this in a production app because any OS X update could break your code (since the methods weren't supposed to be "messed with" by developers anyway).

----

It involves NSThemeFrame and friends; there's an NSDrawerFrame or some such subclass. AndyMatuschak has investigated much of this with his HUD views.

