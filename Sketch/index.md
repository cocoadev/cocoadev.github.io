---
layout: page
title: Sketch
---

The Sketch sample code produces a fairly complete scriptable text and graphics application with lots of functionality, an MVC architecture and undo. The project is located at:

* http://developer.apple.com/library/mac/#samplecode/Sketch/Introduction/Intro.html


AppleScript Support (from the ReadMe.txt file)
-------------------
Sketch is scriptable.  It contains a scriptSuite and scriptTerminology that define its scripting terminology (building on the Core suite and Text suite defined by the scripting frameworks).  It also contains code in the SKTDocument and SKTGraphic classes to support scripting.
 
Most of the code is pretty simple stuff that defines keys for scripting, but Sketch does have some examples of more advanced Scripting support.  
 
In particular the SKTDocument class defines several keys (circles, rectangles, lines, images, textAreas) that are really just subsets of the graphics key.  It includes special NSScriptObjectSpecifier evaluation code to allow scripts to refer to things like "the rectangle after the first circle" or "the graphics from circle 1 to line 5".  These types of specifiers can not be directly evaluated by the default scripting machinery, but they make sense, so Sketch handles them specially.


-----

HowToSupportAppleScript

