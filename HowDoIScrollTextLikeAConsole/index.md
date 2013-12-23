---
layout: page
title: HowDoIScrollTextLikeAConsole
---

**Question: I am trying to implement a console-like display with scrolling  
behavior similar to Xcode's debug console and build windows: as  
text gets added to the view, and the view is already at the end, the view  
scrolls the new text into view, otherwise the view just stays in the same  
place respecting the user's chosen scroll position.
**


**Answer:**
This code (taken from Apple's documentation) appends text to the end of a text view and scrolls the view to make the appended text visible.

    
    NSTextView     *myView;
    NSString          *myText;
    NSRange         endRange;

    endRange.location = myView textStorage] length];
    endRange.length = 0;

    [myView replaceCharactersInRange:endRange withString:myText];
    endRange.length = [myText length];

    [myView scrollRangeToVisible:endRange];



To avoid automatically scrolling if the user has chosen a scroll position different from the end of the text, compare the text view's -visibleRect to its -bounds.  If the visible rect is at the end of the bounds, the user can see the end and autoscrolling is appropriate.  
Otherwise the user has a different scroll position that should be respected.

    
    [[NSTextView     *myView;
    NSString          *myText;
    NSRange         endRange;
    BOOL             shouldAutoScroll;

    // This assumes text view is flipped so max Y is at the bottom
    shouldAutoScroll = ((int)NSMaxY([myView bounds]) == (int)NSMaxY([myView visibleRect]));

    endRange.location = [[myView textStorage] length];
    endRange.length = 0;

    [myView replaceCharactersInRange:endRange withString:myText];

    if(shouldAutoScroll)
    {
        endRange.length = [myText length];
        [myView scrollRangeToVisible:endRange];
    }


**
One of the reasons I love the Mac community is that developers  
think of things like not auto-scrolling a text view if I have  
scrolled up and am reading something.  It is this attention to detail  
that makes the platform nice.  I contrast this experience with  
certain other platforms where the focus is stolen from the user  
frequently and such niceties as respecting my chosen scroll position  
are ignored.
**

