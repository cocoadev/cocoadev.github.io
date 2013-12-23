---
layout: page
title: FieldEditorIssues
---

See also FieldEditor.

**
The short story is that dynamically resizing a fieldEditor is tricky and error prone.

Here's my completed test program that got it working like i wanted, which is to say, it works like Finder.
**

My ultimate goal was to make a text view resize itself to be the minimum amount required (up to a max width), including when wrapping makes things narrower, such as when "Hello Worl" fits on one line, but "Hello World" does not.  When you type the "d", the text view should shrink down to the width of whichever is wider, probably "World".


* Make a new cocoa project.
* Edit the window to have a custom view.
* Make a subclass of NSView called FieldEditorTestView.
* Set the view's custom class to FieldEditorTestView.
* in PB, put the following code in FieldEditorTestView.m


    

#import <Cocoa/Cocoa.h>

@interface FieldEditorTestView : NSView
{
    NSRect itemRect;
    NSString *itemTitle;
    NSSize maxSize;
    NSTextView *mEditor;
}
@end

@implementation FieldEditorTestView

- (BOOL)isFlipped
{
    return YES;
}

- (BOOL)isOpaque
{
    return NO;
}

- (void)setString:(NSString *)s
{
    [itemTitle release];
    itemTitle = [s retain];
    itemRect.size = [itemTitle sizeWithAttributes: nil];
    if (itemRect.size.width > maxSize.width)
        itemRect.size.width = maxSize.width;
}

- (void)awakeFromNib
{
    maxSize = NSMakeSize(128, 300);    
    itemRect.origin = NSMakePoint(100, 100);
    [self setString: @"Hello World"];
}

-(void)drawRect:(NSRect)r
{
    [[NSColor windowBackgroundColor] set];
    NSRectFill(r);
    
    if (mEditor == nil) {
        [[NSColor textBackgroundColor] set];
        NSRectFill(itemRect);
        
        [[NSColor textColor] set];
        NSMutableParagraphStyle *style = [[[NSMutableParagraphStyle alloc] 
            init] autorelease];
        [style setLineBreakMode:NSLineBreakByTruncatingMiddle];
        NSMutableAttributedString *truncatedMiddle = 
            [[NSMutableAttributedString alloc] initWithString:itemTitle];
        [truncatedMiddle addAttribute:NSParagraphStyleAttributeName value:style 
                                range:NSMakeRange(0, [itemTitle length])];
        
        [truncatedMiddle drawInRect:itemRect];
        NSFrameRect(itemRect);
        
        [[NSString stringWithFormat: @"%g,%g", itemRect.origin.x,
            itemRect.origin.y] drawAtPoint: NSZeroPoint withAttributes: nil];
    } else {
        NSResponder *resp = self window] firstResponder];
        if ([[self window] isKeyWindow] &&
            [resp isKindOfClass:[[[NSView class]] &&
            [(NSView *)resp isDescendantOf:self]) {
            [NSGraphicsContext saveGraphicsState];
            NSSetFocusRingStyle(NSFocusRingOnly);
            NSRectFill([mEditor frame]);
            [NSGraphicsContext restoreGraphicsState];
        }
    }
}

- (void)fixEditor
{
    [mEditor setFrameSize:NSMakeSize(128, 10000)];
    [mEditor sizeToFit];
    NSRect r = [mEditor frame];
    [mEditor setFrameOrigin:NSMakePoint(NSMidX(itemRect) - r.size.width / 2, r.origin.y)];
    [self setNeedsDisplay:YES];
}

- (void)startEditing:(id)sender
{
    mEditor = (NSTextView *)self window] fieldEditor:YES forObject:self];
    [mEditor setDelegate:self];
    [mEditor setHorizontallyResizable:YES];
    [mEditor setVerticallyResizable:YES];
    [mEditor setFrameSize:[[NSMakeSize(128, 10000)];
    mEditor textContainer] setContainerSize:[[NSMakeSize(128, 10000)];
    mEditor textContainer] setHeightTracksTextView:NO];
    [[mEditor textContainer] setWidthTracksTextView:NO];
    [mEditor setString:itemTitle];
    [mEditor selectAll:self];
    [mEditor setFrameOrigin:itemRect.origin];
    [self addSubview:mEditor];
    [[self window] makeFirstResponder:mEditor];
    [self fixEditor];
}

- (void)textDidChange:([[NSNotification *)notification;
{
    [self fixEditor];
}

- (void)textDidEndEditing:(NSNotification *)aNotification
{
    // you have to copy the string because it's mutable and gets reused!
    [self setString:[NSString stringWithString:aNotification object] string];
    self window] makeFirstResponder:nil];
    [mEditor removeFromSuperview];
    mEditor = nil;
    [self setNeedsDisplay:YES];
}

- (void)mouseDown:([[NSEvent *)theEvent
{
    NSPoint p = [self convertPoint: [theEvent locationInWindow] fromView: nil];
    if (mEditor != nil) {
        self window] makeFirstResponder: nil];
    }
    if ([[NSPointInRect(p, itemRect)) {
        if ([theEvent clickCount] > 1) {
            [self startEditing:self];
        } else {
            NSSize offset = NSMakeSize(p.x - itemRect.origin.x,
                                       p.y - itemRect.origin.y);
            while (theEvent = self window] nextEventMatchingMask:
                [[NSLeftMouseDraggedMask|NSLeftMouseUpMask]) {
                if ([theEvent type] == NSLeftMouseDragged) {
                    NSPoint p = [self convertPoint: [theEvent locationInWindow]
                                          fromView: nil];
                    [self setNeedsDisplayInRect: itemRect];
                    itemRect.origin.x = p.x - offset.width;
                    itemRect.origin.y = p.y - offset.height;
                    [self setNeedsDisplayInRect: itemRect];
                    [self setNeedsDisplayInRect: NSMakeRect(0,0,100,20)];
                    
                }
                else break;
            }
        }
    }
}

@end



-- TomWaters

Nice , Tom. I bet a lot of tweaking  went into that!
----
I was also trying to get the field editor to dynamically resize, and it was continuously setting the  position of the textview in it's frame too high; that is, every newline insertion meant that the first line was getting clipped off the top while the lowest line was actually showing two lines too high.  self currentEditor] sizeToFit]; works, the rest of [[FixEditor wasn't necessary for me (but then again I do alot of drawing refreshing before then too).  So Anyway, this is great code and keep your eye on fixEditor, if you've come here with field editor vertical alignment problems.  (yes, I did write this post just to add keywords to this page for searches).  thank you for this code!

----

It seems to me that the field editor is more trouble than it is worth. Can I get a witness? �DustinVoss

*Wouldn't AppKit then have to maintain a separate NSTextView instance for each text field in the window? NSTextView isn't the most efficient class in the framework, so that could get costly.*

That's the point of the field editor. It's one NSTextView that is switched around to service multiple NSTextViews - only one has focus at a  time so one field editor is needed. 

You can use more than one field editor if you need it by giving your own NSTextView to a  control instead of having it use the default. This might be needed when you want to drag text from one control to another - one field editor as the source, for for the destination. Still you need only two, not one for each control.

----

If the NSTextView is heavy-weight and the NSTextField is light-weight, the idea of having the latter re-use one instance of the former (like NSColorWell's re-use a shared color panel etc.) is good, but the field editor design is IMHO not!

It breaks both encapsulation and data-abstraction by having the window (or window delegate) return the field editor to the view object which needs it. This design is also broken because e.g. a table view may need _different_ field editors for the various columns. And it complicates the task of figuring out which view is currently the first responder (since that's always the same field editor).

In addition the framework makes assertions about the field editor being an NSTextView instance (and the object value being a string), and there's �behind the scenes� stuff going on, which is undocumented and thus hard to reproduce (when one wants to extend the system).

IMHO the field editor should be an implementation detail of NSTextField and not be exposed like it currently is. Classes like the table view should have a real API for providing custom cell editors and/or the actual NSCell class (and     setUpFieldEditorAttributes: is not it), NSTextField should transparently forward messages to the (potential) field editor used, and the first responder should be the view which initiated the editing.

----

The FieldEditor does *not* complicate the task of figuring out which view is currently the FirstResponder. Figuring that out is extremely easy:     [window firstResponder], and the FieldEditor does not change this.

What the FieldEditor complicates is the relationship between the FirstResponder and the currently focused control. I agree that this should be made easier, but via some other mechanism that explicitly returns the focused control. The FirstResponder is just the object which gets first crack at event and action messages, and that certainly should be the FieldEditor when one is present.

*I'm not sure if you're pedantic or missed my point. The problem is that the field editor is the first responder, which is not what one would expect after calling     [window makeFirstResponder:someTextField], especially in a subclass where you'd like to know �am I the active view� -- and would think the following would work:     if(self == self window] firstResponder]).*

----

I am bringing up a field editor for a custom control.

My problem is that the top margin seems to be zero, unless it has been used for another (system) control first.

But I have tried to query textContainerInset, but it reports {0, 0}.

So what other options is there to control the inset (basically this is for a normal [[NSTextView, which is the field editor)?

----

I'm pretty sure this has come up before - search cocoabuilder.com.

----

Does anyone know how to make the text that is drawn normally (ie. when not being edited) selectable?
I've so far been unable to find anything in the documentation or on Google for this.

*Since the FieldEditor is the object that handles selection, it's doubtful that this is possible.*

Hmmm. That makes sense. he only other thing I can think of would be to replace the drawn string with an NSTextField. Now because this is supposed to be a lightweight object it wouldn't take much memory, and it could be selectable but not editable, then when it wants to be edited I could pass it the custom field editor as above. Does this sound right?

----

If you want it to be edited, just use an NSTextField and let it be edited.  No reason to disable editing and then manage a custom field editor yourself.  If you want to use a custom field editor, then see CustomFieldEditor.

----

So I just found another not-so-fun quirk with the much-loved fieldEditor/NSTextView. If you're trying to figure out the size of a bit of text (    fieldEditor layoutManager] usedRectForTextContainer:[fieldEditor textContainer.size) and the window isn't (or maybe it's that it can't be in my case) on-screen, it doesn't work (returns 0,0). And if you can't bring it on-screen (mmm, requirements), how do you measure the usedRect reliably? You can see what I mean by making a Foundation tool, make a nib with a NSTextView in a window, load it (use NSNib) and try to figure out the size of the text as it exists in the NSTextView - no fair calling     [NSApp run] or bringing the window forward, it has to run like a CLI app normally would. I can produce a test-project if required.

I know it's a weird case but a valid one, imo. Anyone have any ideas for a workaround or things I might be doing wrong? It is possible that I'm missing the obvious (again) even though I've used the same basic code dozens of times by now (though only in GUI-based apps).

...and yes, I've tried using my textView outlet in place of fieldEditor. Assume I've tried all the obvious stuff and give me the benefit of the doubt. ;)

----

Of course I find the fix moments after posting; sizeToFit before you ask it for the size. And I feel stupid yet again - today is a good day! ;)

