---
layout: page
title: NSBox
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSBox_Class/Reference/Reference.html

An NSBox is basically a User Interface element. Its actually a simply NSView that can draw a border around itself and give itself a title. 

There are many methods that can be applied to the NSBox. A handy method is getting the frame - (NSRect)borderRect which can then be used to perhaps resize a window that it is in to the NSBox's size or even move the NSBox around on the screen.

When placing items in an NSBox do drag select, double click the NSBox. To drag the  NSBox, deselect it and click once. In this mode you can also easily change the size of the NSBox.

If need be, the NSBox can be a handy container for moving objects between different projects. Simply select the NSBox, copy and paste into a new project.

----
Note: Inside/behind the Border there is a content-View, and the usable size depends of the type of the border. If you drag something inside the NSBox, it will get positioned inside this content-View. If you want to find it in your code, get the content-view first.

----
Whenever you need selection feature for your boxes, take a look at DZSelectableBox class.
http://www.devzero.it/2009/12/selectable-boxes/

