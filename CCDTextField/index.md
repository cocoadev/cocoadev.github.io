---
layout: page
title: CCDTextField
---

CCDTextField is similar to NSSearchField.

Want to do something similar to iChat's input field? This is a good jumping off point. I'll put up a screenshot when I get a chance unless someone else beats me to it.

Requires: CCDTextFieldCell and CCDPTextView

    
// CCDTextField.h
#import <AppKit/AppKit.h>
#import "CCDTextFieldCell.h"
#import "CCDPTextView.h"

@interface CCDTextField : NSTextField
{
    NSProgressIndicator *progressIndicator;
    NSTimer *progressTimer;
}

- (void)setProgressIndicator:(NSProgressIndicator *)indicator;
- (NSProgressIndicator *)progressIndicator;

// Use these to start/stop the progress animation, thanks.
- (void)startAnimation:(id)sender;
- (void)stopAnimation:(id)sender;

@end

// Feel free to expand this or link to related categories.
@interface CCDTextField(CCDTextFieldAdditions)
- (void)insertText:(NSString *)text;
- (NSRange)selectedRange;
- (void)replaceTextInRange:(NSRange)aRange withString:(NSString *)string;
@end


    
// CCDTextField.m
#import "CCDTextField.h"

@implementation CCDTextField

- (id)initWithCoder:(NSCoder*)coder {
    if (self = [super initWithCoder:coder]) {
        NSTextFieldCell *oldCell = [self cell];
        CCDTextFieldCell *myCell = [[CCDTextFieldCell alloc] initTextCell:[oldCell stringValue]];

        // Did I forget anything?...
        [myCell setAlignment:[oldCell alignment]];
        [myCell setFont:[oldCell font]];
        [myCell setControlSize:[oldCell controlSize]];
        [myCell setControlTint:[oldCell controlTint]];
        [myCell setEnabled:[oldCell isEnabled]];
        [myCell setBordered:[oldCell isBordered]];
        [myCell setBezeled:[oldCell isBezeled]];
        [myCell setBezelStyle:[oldCell bezelStyle]];
        [myCell setSelectable:[oldCell isSelectable]];
        [myCell setContinuous:[oldCell isContinuous]];
        [myCell setSendsActionOnEndEditing:[oldCell sendsActionOnEndEditing]];
        [myCell setEditable:[oldCell isEditable]];
        [myCell setTarget:[oldCell target]];
        [myCell setAction:[oldCell action]];

        // doesn't support a placeholderString..yet
        
        [self setCell:myCell];
        [myCell release];
    }
    {
        NSProgressIndicator *defaultIndicator = [[NSProgressIndicator alloc] initWithFrame:[self frame]];
        [defaultIndicator setStyle:NSProgressIndicatorBarStyle];
        [defaultIndicator setIndeterminate:YES];
        [defaultIndicator setBezeled:NO];
        [defaultIndicator setFrameOrigin:NSMakePoint(-600,-600)];
        [self setProgressIndicator:defaultIndicator];
        [defaultIndicator release];
    }

    return self;
}

- (void)dealloc
{
    [self setProgressIndicator:nil];
    [super dealloc];
}
 
- (void)setProgressIndicator:(NSProgressIndicator *)indicator
{
    [progressIndicator removeFromSuperview];
    progressIndicator = indicator;
    [self addSubview:progressIndicator];
}
- (NSProgressIndicator *)progressIndicator
{
    return progressIndicator;
}

- (void)startAnimation:(id)sender
{ // Game on
    progressTimer = [[NSTimer scheduledTimerWithTimeInterval:[progressIndicator animationDelay] target:self selector:@selector(animate:) userInfo:nil repeats:YES] retain];
    [progressIndicator startAnimation:sender];
    self cell] setAnimating:YES];
}
- (void)animate:(id)sender
{
    if ([[self cell] isAnimating]) [self display];
    else
    {
        [progressTimer invalidate];
        [progressTimer release];
         progressTimer = nil;
    }
}
- (void)stopAnimation:(id)sender
{ // Game off    
    [progressIndicator stopAnimation:sender];
    [[self cell] setAnimating:NO];
    [self removeFromSuperview];
}

- (void)mouseDown:([[NSEvent *)event
{
    NSPoint mouseLoc = [event locationInWindow];
    NSRect bounds = [self bounds];
        id cell = [self cell];

        if (!NSPointInRect(mouseLoc, [cell textRectForBounds:bounds])) {
            
            NSRect leftRect = [self convertRect:[cell leftButtonRectForBounds:bounds] toView:nil];
            NSRect rightRect = [self convertRect:[cell rightButtonRectForBounds:bounds] toView:nil];
                
                if (NSPointInRect(mouseLoc, leftRect)) {
                    id leftCell = [cell leftButtonCell];
                        [leftCell setHighlighted:YES];
                }
                else
                if (NSPointInRect(mouseLoc, rightRect)) {
                    id rightCell = [cell rightButtonCell];
                    [rightCell setHighlighted:YES];
                }
                return;
            }
     
     [super mouseDown:event];
}

- (void)mouseUp:(NSEvent *)event
{
    NSPoint mouseLoc = [event locationInWindow];
    NSRect bounds = [self bounds];
    id cell = [self cell];
    id leftCell = [cell leftButtonCell];
    id rightCell = [cell rightButtonCell];
    
        [leftCell setHighlighted:NO];
        [rightCell setHighlighted:NO];
    
    if (!NSPointInRect(mouseLoc, [cell textRectForBounds:bounds])) {
        NSRect leftRect = [self convertRect:[cell leftButtonRectForBounds:bounds] toView:nil];
        NSRect rightRect = [self convertRect:[cell rightButtonRectForBounds:bounds] toView:nil];
        
        if (NSPointInRect(mouseLoc, leftRect)) {
            if ([leftCell isKindOfClass:[NSActionCell class]])
                    [self sendAction:[leftCell action] to:[leftCell target]];
        }
        else
            if (NSPointInRect(mouseLoc, rightRect)) {
                if ([rightCell isKindOfClass:[NSActionCell class]])
                    [self sendAction:[rightCell action] to:[rightCell target]];
            }
    }
    
    [super mouseUp:event];
}

@end

#pragma mark (CCDTextFieldAdditions)
@implementation CCDTextField (CCDTextFieldAdditions)
- (void)insertText:(NSString *)text
{
    [self replaceTextInRange:[self selectedRange] withString:text];
}
- (NSRange)selectedRange
{
    id textObj = self window] fieldEditor:YES forObject:self];
    return [([[CCDPTextView*)textObj prvtSelectedRange];
}

- (void)replaceTextInRange:(NSRange)aRange withString:(NSString *)string
{
    NSMutableString *ourString = self stringValue] mutableCopy];
    [ourString replaceCharactersInRange:aRange withString:string];
    [self setStringValue:[[ourString copy] autorelease;
    [ourString release];
}
@end


----
11/21; The cell is highlighted and the action is sent in     mouseUp instead of     mouseDown:.
*This should be fixed to deal with different kinds of NSButtonCells - it's no good for NSPopUpButtonCells, for instance. Suggestions or fixes?*

11/25; Added the category to make inserting text easier. Getting the selectedRange requires posing as the field editor for some reason so CCDPTextView is required (unless you can find another way or want to rip it out).

11/29; Removed the conversation with myself and added the optional progress bar. Use     [textfield startAnimation:nil]; to start the progress bar and     [textfield stopAnimation:nil]; to stop it. Only tested with an indeterminate progress bar, you can fix the others, right?

Forgot     [myCell setAlignment:[oldCell alignment]];, seriously, are there more? Removed the placeholder stuff since it was being drawn over anyway.

4/8; CCDImageCategory wasn't needed, as described on its page.

----

Sorry to be a drag but ... what is CCDTextField for? What does it do? The explanation "Want to do something similar to iChat's input field?" doesn't tell me what this is, does. -- MikeTrent

It's a text field that allows for the additions of buttons in the right and left portions of the field, such as a search field or the iChat text field with the smiley face button on the right of the field.

----

The explanation can be misleading for some, especially those looking for a DynamicallyResizingTextViewOrField, another behavior of iChat's input field. 

----

Does this also work with a subclass of NSSecureTextField. I tried this with other examples, but the icon never appeared :(

