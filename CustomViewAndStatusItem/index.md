---
layout: page
title: CustomViewAndStatusItem
---

Little issue I am having.

I have a subclass of NSView which I am drawing text into. I have it instantiated and linked to it from another class as an outlet. In the class I am creating an NSStatusItem which I want to set the customview as the view using setView. Unfortunately it seems not to be working. I know I have to allocate it or something close, but I am not sure right now. Can anyone assist?

----

Make sure you retain your NSStatusItem when you call NSStatusBar's     statusItemWithLength: method.

