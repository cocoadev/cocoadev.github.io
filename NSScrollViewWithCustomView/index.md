---
layout: page
title: NSScrollViewWithCustomView
---



I am trying to create an interface where a Window will contain a NSScrollView and within the scroll view, a custom view will be contained. It is similar to how the 'Preview' application operates: An NSScrollView object takes up the entirety of the Window, and when the window is resized or the custom view inside the scoll view becomes to large, scrollbars appear in the scroll view.

I have tried to make this in Interface Builder, but I am having problems, specifically:

a) The scrollbars will appear when it is not necessary for them to appear (I have the 'Automatically Hide Scrollbars' option turned ON)
b) When I resize the Window to be larger, the scroll view's size does not adjust to the window's size, so it just remains the original size that it was, (how do I make it always match the size of the window, even when resizing?)
c) When I resize the Window to be smaller, and then larger, the resize icon in the bottom right corner is painted onto my custom view and remains there

Simply, I don't know what I am doing with the scroll view. I'm sure there are plenty of apps that have this same functionality; I'd like to take a look at how they have accomplished this, but I haven't found any good examples.
----
More information would help. For example, how are you creating these views? Are you setting them up in a nib with Interface Builder, or are you creating them programmatically? If the latter, show us your code. Generally, see HowToAskQuestions. Absent more details, here's what I can tell you:

*a) I'd suspect that your custom has a problem related to size. Are you overriding something like -setBounds: or -setFrame: without calling the inherited version? Use the debugger to check the size of your custom view whenever the scroll view displays the scroll bars and you think it shouldn't.
*b) Setting up autosizing is a fairly basic Interface Builder operation. Rather than explain it here, I'll point you to Apple's documentation: http://developer.apple.com/documentation/DeveloperTools/Conceptual/IB_UserGuide/EditingNibFileObjects/chapter_5_section_4.html
*c) Sounds like your custom view isn't redrawing in that area. This may be related to the size problem in (a), for example if your view is actually larger than you think it is, you may not be redrawing in the additional area. Again, the debugger could help here: try breaking on -drawRect and check the rectangle that's passed in. Or you could fill the rect with a color early in your -drawRect to give yourself a visual indication of the area that's supposed to be  redrawn; try adding the following to the top of your -drawRect routine:
    
[[NSColor redColor] set];
[NSBezierPath fillRect:rect];


Hope that helps. -CS
----
I think I understand. You are saying that you are happy using custom NSView<nowiki/>s in windows, and through Interface Builder you can get the desired resizing behaviour you want. However, now you want to use an NSScrollView to do your drawing and things don't look as tidy. I'm guessing the behaviour you want is to set the view to be a particular size and then, when the window is smaller than the view, the scroll bars to appear. When the window is greater than that size you want the scroll bar to disappear.


* a) However, the grey 'runners' for the scroll bars will not disappear with NSScrollView (as far as I'm aware). I think the 'Automatically Hide Scrollbars' option will just remove the blue bars that move on the grey runners.
* b) Given you description for point b, *"how do I make it always match the size of the window, even when resizing?"* it sounds like you want your NSScrollView to behave like a NSView. I think it is best to use NSScrollView when you know the view to be bigger than the containing window. In this way you don't want it to 'resize' in the traditional sense. What might be an idea is to stop the window from being resized pass the bounds of the NSScrollView. But I'm not too sure how to do this? 


I'm used NSScrollView for the first time today, this is what I have understood so far about them.

-- DJF

----

No, no, that's not right. If you check the 'Automatically Hide Scrollbars' option, the runners go away. The blue bars go away anyway when the view is large enough to show everything. What you want is a scroll view that autoresizes horizontally and vertically with the 'Automatically Hide Scrollbars' option. The view inside it should not autoresize at all. The tricky part is keeping the inside view centered when the window is too big; someone else want to chime in about that?

CS did actually give the right advice. Did you *try* it? Especially the bit about checking your     drawRect: to see where the view *actually* ends (which would cause the scroller problem). --JediKnil

----

DJF, you are aware than NSScrollView *is* a subclass of NSView, I hope?  And that it can be autoresized just like any other NSView?

----

It's not I who asked the initial question. Thanks for putting me right on those points I made though. --DJF

----

First, I actually think the original poster has much more basic problems than the NSScrollView specific ones discussed here. To me it seems, he/she should first try to get things straight with Interface Builder, view resizing in IB, subviews and so on and so forth...

However, as the point was raised, to center the custom view inside the scroll view, there has been a discussion here on cocoadev recently (CenteringInsideNSScrollView) where different strategies to achieve that have been outlined- I also threw in my few pence for the special case of an image view (like in Preview).

--MarCocoa

