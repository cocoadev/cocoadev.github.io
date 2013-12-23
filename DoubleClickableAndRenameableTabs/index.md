---
layout: page
title: DoubleClickableAndRenameableTabs
---

I'm looking for a way to implement a tab-control that catches a double-click on the tabs and then opens a lineeditor in place (a sheet would work too, while it wouldn't be as pretty) which then
allows me to rename the tab.

For the curious: I'm a big fan of iTerm, as it works similar to what I'm used to under Linux/KDE. KDE's Konsole allows to to easily rename tabbed terminal sessions - which makes working with several sessions way easier.

So far, I'm pretty clueless. I know how to override mouseDown: to capture some mouse events - but unfortunately, the double click seems not to be a build-in event type. But e.g. the finder certainly uses it, so any ideas how to capture one?

----
Check out the     clickCount method.

