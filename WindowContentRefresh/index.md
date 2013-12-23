---
layout: page
title: WindowContentRefresh
---



How do i update the content of a window with code?

----

I think you're going to have to be a little bit more specific than that. What are you trying to do?

----

I have a button in a window. Once in a while i change the frame of the button from the General/AppController, but the changes dont show up in the window.

So i figured i had to update the contentView, but i dont know how.

I've tried a couple of things, so far [window setContentView:[window contentView]]; are the only thing that work.

----

    General/window contentView] setNeedsDisplay:YES]

----

tnx, i thought i'd tried that

----
Don't unneccessarily update views that haven't changed. In this case, if only the button changed, send     [myButton setNeedsDisplay:YES] -[[EnglaBenny

*he says he's moving the button though, in which case the entire content view may need updating*

Then only setNeedsDisplayInRect in the old frame

*let's not confuse the boy :p*

I'm curious as to why you have to manually refresh the thing in the first place. Normally, redraws as a result of frame changes just work without any manual intervention.
