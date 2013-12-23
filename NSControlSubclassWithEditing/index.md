---
layout: page
title: NSControlSubclassWithEditing
---

Can anyone say how to have a cell inside an NSControll instance that has several NSTextFieldCells in it that can be edited.  Please don't suggesting using a matrix.  I need my own composite cell with editing and there are just no two ways about it.  I've scoured the web, (including cocoadev) but have not found any source examples.  Apple doesn't seem to provide anything other than the venerable clock control, but that has no text editing to speak of.  Thoughs?  ideas?  Examples?  Thanks!

----

I don't entirely understand what you're asking. If you want to know how to make an NSTextFieldCell inside a custom control work, it should just work properly as long as you pass the proper events to it. If you want to know how to make a custom NSCell subclass be editable, look at the FieldEditor.  

I've looked at the field edit, and all I hear is people better "dime to dollars" that you don't want to have to deal with the FieldEditor.  A brief example of an NSCell subclass that does text editing would be really helpful.  If anyone knows where to find such an example -- or has one ready made -- that would be great.

