---
layout: page
title: NSButtonCell
---

NSButtonCell is the cell class that goes hand-in-hand with NSButton or NSMatrix to create clickable buttons. A NSButtonCell object contains methods for drawing the button, tracking the mouse cursor, and sending an action method to a target. Normally you should subclass NSButtonCell (instead of NSButton) when you need to customize the drawing or behavior of a button. If you're looking at the NSButtonCell documentation for the first time, remember that it inherits much of its functionality from its parent classes, NSActionCell and NSCell.

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSButtonCell_Class/Reference/Reference.html

You can use NSButtonCell in your own classes. Read on for more information. Keep in mind that the following discussion is based around using NSButtonCell in an NSView class; unless you have a good reason to do otherwise, you should subclass NSControl.

----

I'm working on a subclass of NSTabView, and I'm using an NSButtonCell inside it as a "close tab" button. I have it working as it's supposed to, except for drawing the "alternate" button image / border when it's pressed. Here's what I've done so far, in the NSTabView subclass:

1) Create the button cell and set the attributes:
    
[_closeTabButtonCell setBordered:NO];
[_closeTabButtonCell setImagePosition:NSImageOnly];
[_closeTabButtonCell setButtonType:NSMomentaryChangeButton];
[_closeTabButtonCell setTarget:self];
[_closeTabButtonCell setAction:@selector(closeThis:)];
[_closeTabButtonCell setImage:[NSImage imageNamed:@"CloseTab"]];
[_closeTabButtonCell setAlternateImage:[NSImage imageNamed:@"CloseTabAlt"]];


2) Draw the button cell in my drawRect method:
    
[_closeTabButtonCell drawWithFrame:buttonRect inView:self];


3) Start tracking with the button in mouseDown:
    
[_closeTabButtonCell trackMouse:theEvent inRect:buttonRect ofView:self untilMouseUp:NO];


So far, everything works except for re-drawing the button cell when it's clicked-- drawing the "normal" button state works fine, and it properly tracks the mouse and fires off the correct action message when it's clicked. However, the cell does not redraw itself using the "alternate" state when it's clicked, and I'm not sure where this is done-- it seems like the button cell should make the control view redraw in the trackMouse: method, but this isn't happening. Trying to display myself in mouseDown obviously doesn't work, since the trackMouse: method takes control until the mouse click is finished.

I've read the conceptual and API docs a few times over now, and I've pretty much hit a wall; if anyone can point me in the right direction, I would appreciate it.

----

Hm.  This might be hard.

(1) The pressed look is achieved by calling     -[NSCell highlight:withFrame:inView].  Try calling that before and after you call trackMouse::::.  I don't think it'll be all you need, but it might get you moving again.  Also, the cell cannot ask the control to draw it unless     -[NSCell controlView] is set.  The cell's draw method will set the controlView, but if the timing is wrong then you may need to manually call     -[NSCell setControlView:].

(2) Argument types nonwithstanding, cells can only be used with controls.  There's crosstalk from the cell to the control.  Can you possibly use genuine NSButton subviews instead of NSButtonCells?

Actually, I'd bet you'd do better _not_ to let the button cell handle the mouse tracking.  If you use the button cell purely for drawing (set it up, call a draw method) you can probably get away with NSTabView not being a control.

----

Thank you!     highlight:withFrame:inView was in fact the method I was missing. Calling it directly before and after     trackMouse: properly sets the alternate and normal button image. The only difference between NSButton I can tell is when you move the mouse cursor out of the tracking area and then back over it, but I suppose that's something my NSTabView subclass needs to handle, and not the button cell.

I was worried about NSTabView not inheriting from NSControl at first, but I haven't noticed any weird side effect from it yet-- everything seems to be working okay, at least. I wanted to stay away from using an NSButton if I can, since I'd have to create one for each tab, which I think would end up being pretty heavyweight compared to just a single NSButtonCell. I'll leave this discussion open for a few days in case anyone has any caveats I should know about, then I'll refactor it into NSButtonCell.

----

Well, even if you haven't hit it yet, unfortunately you cannot guarantee that your cell will function (and continue to function in the future) unless the provided controlView is a control.  For example, -[NSCell highlight:withFrame:inView:] calls back to -[NSControl updateCell:].  If this isn't causing a crash for you, it's probably because the controlView happens to be nil.

----

Explicitly setting the controlView before calling     highlight:withFrame:inView does not cause any errors-- I'm not saying you're wrong, but it seems that (at least with NSButtonCell) seems to check whether the     updateCell: selector exists before calling it.

Just to be safe in this situation I'm passing nil into     drawWithFrame:inView:, which the docs say:
*This method draws the cell in the currently focused view, which can be different from the controlView passed in. Taking advantage of this behavior is not recommended, however.*
By doing this it seems that the controlView should always be nil, which I don't think will have any negative consequences in this case.

----

Hello. I am trying to find out the best way to subclass NSButtonCell to add my own bezel style. Is there some way to put my cell into a NSButton IB object? OR, do I need to create a custom subclass of NSButton also, which setCell: [[MyButtonCell alloc] init] in -[MyButton awakeFromNib]? Any help would be appreciated.  (SW)

----
See http://www.mikeash.com/blog/pivot/entry.php?id=17 for a general technique. You will have to subclass NSButton as well, but using the technique in that post it's fairly painless.

----

Hi, I'm trying to make my own buttonCell look by subclassing it, etc. It's all good except that it draws a white background around the roundish image I'm using. All this would be fine simply by using [myButtonCell setBackgroundColor:[NSColor clearColor]]; The problem here is that my NSWindow uses a transparent background, which I texture myself. The white background becomes transparent but also deletes the window texture behind it! This doesn't happen for regular NSButton. How can I solve this? I looked around but couldn't find any helpful information... Thanks a lot.

TeeJay

----
Overriding     - (BOOL)isOpaque to return     NO will give a hint to the caller that some part of the image your button cell draws may be transparent.

----
Given an NSButtonCell object, how can one programmatically distinguish between a checkbox and a radio button?  Checkboxes and radio buttons both return the same values for showsStateBy and highlightsBy.
----
There is no effective way.
Whether an NSButtonCell behaves as a check box or a radio button is not a property of the NSButtonCell.  It is a property of the NSMatrix in which the NSButtonCell is used.  The images used with NSButtonCells are not reliable determinants and are irrelevant to the button selection behavior within a matrix.
----
If this is the case, what is the best approach to determine how to draw a custom NSButtonCell subclass?  I'm developing a subclass of NSButtonCell that handles all possible configurations, and am stuck at this point.
----
I don't know the answer, but one way to find out would be to create one of each kind, then compare the contents of the two objects in the debugger. When you find out which ivars are different, that should hopefully lead you to public accessors for the properties which they represent.
----
The easiest you can do is     [cell valueForKey: @"buttonType"]. There is no public method for this, but apparently there must be a hidden one. Just add this to your code:
    
@interface NSButtonCell (NSButtonCellPrivate)
- (NSButtonType)_buttonType;
@end

Then you'll be able to call something like this:     [cell _buttonType] to get the same result as above.
----
How i may change color Button for round rect Button.? Exapmle the Button "empty Trash" in a top right coner when trash opned.

