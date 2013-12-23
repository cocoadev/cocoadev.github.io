---
layout: page
title: HelpWithNSSplitView
---

I going crazy trying to figure out how to constrain the minimum size of a subview in a split view. I can control the size without any problem with constrainSplitPosition and constrainMinCoordinate in a delegate but these two methods do not control resizing when the window is resized (for that your delegate is supposed to respond to the splitView:resizeSubviewsWithOldSize: method) 

    
- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize {
    NSRect leftFrame=[leftView frame];
    NSRect rightFrame=[rightView frame];
    NSRect newFrame=[sender frame];
    float dividerWidth=[sender dividerThickness];
    if (sender==baseSplitView) {
           [baseSplitView adjustSubviews];
      	if (frame.size.width<450) {
                frame.size.width=450;
                sideFrame.size.width=newFrame.size.width-frame.size.width-dividerWidth;
           }
           [leftView setFrame: leftFrame];
           [rightView setFrame: rightFrame];
    }
}




This doesn't work smoothly, the left view will resize in a haphazard manner. Anyone familiar with this NSSplitView delegate method? Oh yeah, I've already tried adjusting the subviews before and after setting the frames of the subviews.
----

see NSSplitView --zootbobbalu

----

I have created a webview inside a NSSplitView however when I compile and look at the view it does not have the 1 pixel grey line around the edge as a NSTableView does (I have one of these in my other split view pane). and looks as though it's sitting flat on the window (it's a metal window) instead of looking slightly sunken like the NSTableView.

My question is this - do I have to draw this myself or is there a simple way to get this grey border? To be clear this isn't the focus ring it's the standard grey border that some views seem to have.

----

Put the webview in a NSBox (don't forget to set the resize flags on the webview), even if you draw the line programcally you will still have the scroll bar partially off the window.

----

I've been examining the iLife & iWork suites, and am trying to emulate some of the UI. I love the new UI that Mail.app uses in Tiger. So far I have this:

Main window in document.nib contains: menuPlaceholderView NSView placeholder instance.
document.nib contains: menuView -  NSView instance held in the Nib that that holds the layout / UI for the menu section of the window.

document.m ...

awakeFromNib
{
     [menuPlaceholderView setAutoresizesSubviews:TRUE];
     [menuPlaceholderView addSubview:menuView];
}

So this loads my custom menu view into the placeholder NSView instance in my main document window.  Also I think this should set the placeholder view to automatically tell its subviews to resize.

Mail.app doesn't seem to use an NSSplitView for its menu.  Rather it has a drag handle at the bottom of the view. I broke out Apple's UI Element Inspector to see what they were using for the resize drag handle.  All it tells me is that it's the main window.  Not an NSView instance or anything.  It looks just like the NSButton instances next to it, but it's not.

I know I'll need to use methods for NSView to actually handle the drag resizing, but I don't know how to go about catching the drag or what object I'm dragging.  Can anyone maybe help point me in the right direction?

Thanks, CliffPruitt

----

Subclass an NSSplitView and override -[NSSplitView dividerThickness].
Making it 1 pixel wide will make a black line, which can be grabbed in the usual place - the middle. You can't see the little notch, though. You could make a custom button to imitate the grab spot at the bottom. It all just depends on how much you want to do to imitate that look exactly. -- JasonTerhorst

----

In fact you can *easily* do this with some ready-made examples found here and many other places. All it is is a view with a 'draggable' region that sends its movement information to the split view in the form of "make me a pixel wider" or "make me a pixel narrower" (or taller/shorter).

