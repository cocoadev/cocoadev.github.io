---
layout: page
title: WrappingTwoNSCellsIntoOne
---

Hi,
I searched around and couldn't find a straightforward answer to this question so I'm going to ask it again.  My apologies in advance if the answer is already out there.  I am trying to have an General/NSOutlineView where one of my General/TableColumns has cells which have both a checkbox, and an editable General/TextField.  I've managed to get it to display exactly like what I'd like all on my own, but am having trouble whenever one of the rows is clicked.  Here's how I've done things:

    
// General/TaskCell.m:
-(id) init {
  self = [super init];
  
  // Initialize the button half:
  button_cell_ = General/[[NSButtonCell alloc] init];
  [button_cell_ setButtonType: General/NSSwitchButton];
  [button_cell_ setTitle: @"Test"];
  
  // Initialize the text half:
  text_cell_ = General/[[NSTextFieldCell alloc] initTextCell: @"Nothing..."];
  return self;
}

-(void) drawInteriorWithFrame:(General/NSRect) rect inView:(General/NSView*) view {
  // Split up the view into two parts:
  [button_cell_ drawInteriorWithFrame: rect inView: view];
  
  float buttonWidth = [button_cell_ cellSize].width;
  General/NSRect trect = General/NSMakeRect(rect.origin.x + buttonWidth, rect.origin.y, rect.size.width, rect.size.height);
  [text_cell_ drawInteriorWithFrame: trect inView: view];
}


Then, in my General/OutlineView delegate I do the following:
    
  General/NSTableColumn* tc = [outline_view_ tableColumnWithIdentifier:@"Task"];
  [tc setMaxWidth: 1900];
  [tc setDataCell: General/[[TaskCell alloc] init]];


Here is where the problems begin.  The first problem is if I create some rows in the table, they show up perfectly, but then if I click on one (single or double) I get a segfault (or bus error, they're both in the same place) on the first line of     drawInteriorWithFrame in my General/TaskCell.m.  This brings me to my second problem.  I tried printing out the pointers of     button_cell_ and     text_cell_ inside     drawInteriorWithFrame and regardless of how many different rows I have, the two sub-cells always have the same pointers.  That doesn't seem right to me, although perhaps it is, I'm not so familiar with these things.

Then, as future questions, I was wondering if anyone can point me towards how to intercept when a single click occurs on my cell, and how I would either forward that click onto the button half, or highlight the whole row depending on where in the cell the click took place.  I've tried overloading     editWithFrame inView editor textObj delegate event and     performClick and     selectWithFrame inView editor delegate start length and none of those ever seem to get called before my bus error/segfault.  It's possible that those are the right methods to override, but it's just not getting far enough into the process.

Thanks for the help.

----

First problem:  Note that General/NSCell conforms to General/NSCopying.  Did you override -copyWithZone: in your subclass?  I bet you didn't.  Here's what happened:

(1) When you click a table row (and under some other circumstances) General/NSTableView copies your cell.
(2) -General/[NSCell copyWithZone:] contains a call to General/NSCopyObject[1][2], which is a terribly broken function.  It copies the data of an entire object as raw bytes.  If one of your ivars happens to be an object pointer, the pointer will be slammed over without the object being retained.  If all subclasses implement copyWithZone: properly it doesn't matter, but if they don't..
(3) Your dealloc method (properly) releases the button subcell.  Since it didn't receive a retain when the supercell copy was created, the subcell is deallocated prematurely.  Your app crashes the next time it uses the cell.

[1]: http://gemma.apple.com/documentation/Cocoa/Conceptual/General/MemoryMgmt/Tasks/General/ImplementCopy.html#//apple_ref/doc/uid/20000049-997407

[2]: But don't rely on this!  Implementation detail.

Problem 2 (cell reused to draw many rows): Not a problem.  A cell is used like a rubber stamp.  It's configured and drawn, configured and drawn.  Most of the time, one cell is used to draw every row in a column.  Under some circumstances, though, it may be copied.  See problem the first.

Future question: To forward mouse tracking (which is what you want), override -General/[NSCell trackMouse:inRect:ofView:untilMouseUp:].  If the event is within your button subcell's frame, turn around and call [buttonSubcell trackMouse:inRect:ofView:untilMouseUp:].  If it isn't, call super.  (You don't have to highlight the row, General/NSTableView does that.)

----
Hi,
Thanks very much for the advice!  I've added     copyWithZone and it seems to be working fine like this:

**
This was wrong.  I edited it inline rather than leave incorrect code out where someone might copy it.  The receiver of copyWithZone: is the original cell.
**

    
-(id) copyWithZone:(General/NSZone*) zone {
  General/MyCell *copy = [super copyWithZone:zone];
  copy->button_cell_ = [button_cell_ copyWithZone:zone];
  copy->text_cell_ = [text_cell_ copyWithZone:zone];
  return copy;
}


I'm having a slight bit of trouble with the mouse tracking.  I can't figure out how to effectively translate the mouse click position from window coordinates into my General/NSCell's view coordinates.  I've gone so far as to try this:
    
-(BOOL)trackMouse:(General/NSEvent*)theEvent inRect:(General/NSRect)cellFrame ofView:(General/NSView*)controlView untilMouseUp:(BOOL)untilMouseUp {
  General/NSPoint click = [controlView convertPoint: [theEvent locationInWindow]
                                   fromView: General/self controlView] superview;
  ...
}

except that gives me an infinite loop, and is pretty unrobust because if another view gets plopped down between the window view and us we're screwed.  One other strange thing is that if I just forget about deciding if it's hit my button or textfield and just return     [button_cell_ trackMouse....], it still highlights the row even when I click on the button.  Maybe once I get all this tracking sorted out it'll be clear how to only select the row if they click outside the button, and it's not a double click because that would activate the text field.  I think it'll all work fine if I can make it not highlight on button click.

Thanks again for the help.  It's really appreciated.
----

Please see http://developer.apple.com/documentation/Cocoa/Conceptual/General/CocoaViewsGuide/General/WorkingWithAViewHierarchy/chapter_4_section_8.html . Note that [theEvent locationInWindow] is in window coordinates (hence the name).

----
I think the question was answered 8 years ago: http://www.stepwise.com/Articles/Technical/General/NSCell.html
In particular, see _VDataBrowserCell.

----
Hi all.  Thanks for the pointers.  I've figured out how to detect which part of my cell is being clicked, but now things are getting slightly more complicated.  I'm having problems getting and setting information for my combined cell.  When my General/OutlineView delegate has     outlineView: setObjectValue: forTableColumn: byItem: called on it, it's getting passed Nil as the item.  What I've done so far is override     setObjectValue and place the following in it:
    
-(void) setObjectValue:(id) object {
  if ([object isKindOfClass: General/[NSAttributedString class]]) {
    General/NSAttributedString* str = (General/NSAttributedString*) object;
    General/NSNumber* n = [str attribute: General/NSStrikethroughStyleAttributeName atIndex: 0 effectiveRange: nil];
    [button_cell_ setState: [n intValue]];
    [text_cell_ setObjectValue: object];
  } else {
    General/NSLog(@"It wasn't an attributed string");
  }
}

Then, in my outlineView delegate I pass an attributedString.  I'm not sure that this is the way to go about achieving this, but it works for now.  The problem comes when I try and either click on the button, or double click on the text part.  The button doesn't check/uncheck nor does the text begin editing.  I've overloaded     editWithFrame: inView: editor: delegate: event: to forward the request on to the     text_cell_ but no dice there.  So, to sum up, current problems are:
1) Getting nil passed to     outlineView: setObjectValue:...
2) How to edit the text portion of my combined cell via a double click on its area.
3) How to be notified of the button being checked/unchecked.

Thanks again for all the advice.  It's very much appreciated.
