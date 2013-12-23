---
layout: page
title: SwitchingContentViews
---

I'm trying to get a contentview from a window into a panel, and back again as needed. I simply do:
    
if(![panel contentView])
{
   id view=General/window contentView] retain];
   [panel setContentView:view];
   [window setContentView:nil];
   [view release];
}

... and vice versa when I need it back into the window; the first swtich (window->panel) works fine, but when I try to switch the view back to the window, it doesn't work - the contentView stays with the panel, even though it's now assigned to the window (window is devoid of any view). Is there something special about panels that make them keep the view?

----

I would think that windows always have a content view, and that it'll make a new empty one when you try to set it to nil, so that     ![panel contentView] is never going to be true.

*Indeed. You should check to see if     [window contentView] == contentViewA or if     [window contentView] == contentViewB, rather than checking if it's empty or not.*

Wrong. The logging code I've got shows that the if statement is true each time.

 I've also tried setting a global variable to the window's contentView and using that for checking and as a reference, but the result is still the same.

----

Swapping the content view is ugly anyway. Put everything inside a separate custom view, and swap that by using the standard addSubview: and removeFromSuperview methods.

*I've tried this, but then I can't get the window autosaving to work correctly at all.*

What does autosaving have to do with swapping views around?

----
Swapping the content view has worked for me, but I used an internal flag to keep track of which view was in the window. However, I haven't tried displaying the swapped view in a window besides the original. What's the situation, anyway? --[[JediKnil

*Well I was having problems with swapping in/out and external view, because it wouldn't resize right on autosaved windows; I think I solved it by assigning it to the window in awakeFromNib: instead of only just before I showed the window so that it's attached to it before it gets resized when the window's state is restored. Now the window state is preserved the the view gets resized properly too.*
