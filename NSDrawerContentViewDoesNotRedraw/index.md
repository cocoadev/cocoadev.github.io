---
layout: page
title: NSDrawerContentViewDoesNotRedraw
---

My app has a document window with a drawer attached for controlling some
aspects of the content in the document window, but there is a toolbar too.
One operation requires me to orderOut the main window while an auxiliary
window is put in front (I don't want the main window visible
while the aux window is visible - the latter contains a kind of quiz about the
content in the main window.)

Here's the problem:
When I order the main window to return, if the drawer was open, it does
not re-draw itself properly, and neither does the border of the drawer.
But toggling the drawer closed and open again
solves that in a clumsy way.

It looks as if I need to specificallly order the DrawerContentView to re-draw
itself (I guess that's done with setNeedsDisplay, or something).

However, rather than wiring up all the machinery needed to (1) get
a reference to the recalcitrant content view into the controller for the aux window
so I can (2) order the re-draw, and since I'm not sure at this point
that that would really be a compact solution anyway,
isn't it better to just make sure the drawer
is closed automatically before raising the aux window?

If I do that for the user behind the scenes, does it violate any  UI guidelines or other
deeply-cherished principles?

Incidentally, I realize doc-based was perhaps not the ideal way to go for this one.
I have to run the quiz as a modal session, and blow the window up to full-screen
to really circumvent cheating on the test.

On the plus side, I (foolishly, perhaps) like the flexibility of being able to have several
content (document) windows open for study at the same time,
even though the quiz function will only address the content in one of them at a time.

D j S

----

I believe the correct way to do this is to close the drawer when hiding the window and reopening the drawer when showing it again.

--  AndreasMayer

