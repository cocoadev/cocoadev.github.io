---
layout: page
title: NSBorderlessWindowMaskAndTextfields
---

I'm having a peculiar problem with a custom window.  I'm using a window with NSBorderlessWindowMask in order to create my own custom title bar, but for some reason when I use NSBorderlessWindowMask, textfields in the window do not receive focus.  I can click the x to make default text go away, I can paste in it by right clicking, but I can't type in it.  Does anyone know what I'm doing incorrectly/how I can get around this?  I know it has to do with NSBorderlessWindowMask, because when I leave everything else untouched and change this it works.

Thanks in advance,

FranciscoTolmasky

----

Override     - (BOOL)canBecomeKeyWindow to return     YES. By default this returns     NO for borderless windows.

