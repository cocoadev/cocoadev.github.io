---
layout: page
title: EditableTextfield
---

I am working on a subclass of NSTextField to give me functions like the editing of addresses in Address Book. 

I have a small problem though, I always get the focus ring around my object (something that Address Book does not have) and when I call the object to switch off editing, it remains in the editing mode. 

Is there any way to fix these two issues?

----

Address Book uses a rather complex set of subclassed NSTextView, NSLayoutManager, etc. It uses CG calls for the shadows and bevel highlighting...the lists goes on and on. I'm interested in anyone with any idea of a starting ground for this sort of thing because I wouldn't mind having a crack at it myself; I just need to have some idea of where to start.

----

With respect to focus ring drawing, this is from the Panther release notes: *NSView and NSCell have introduced API which allows developers to control focus ring drawing. Specifically, you can disable a view's focus ring drawing [...]*

Yup, am using that now.

