---
layout: page
title: HowCanISupportTablets
---

I want to include tablet support in an application I'm working on, and I'm not talking about inkwell. I'm talking about the kind of support photoshop has. The main problem is that I don't really want to write an IOKit driver (Wacom did that already, and for older tablets TabletMagic works) or deal with anything system-level. Does Cocoa (or any other framework) include an easy way to manage tablets?

----
Check out NSEvent's - (float)pressure method if all you are wanting is the pressure it is sending.  If you want something else, you'll have to be more specific.
----
I think that will do, since most of the time tablets work like mice anyway. Now I just have to figure out how to mess 
with inkwell.

*Retired*

