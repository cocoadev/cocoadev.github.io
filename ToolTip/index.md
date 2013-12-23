---
layout: page
title: ToolTip
---

Cocoa provides only almost static tooltips. When you want to implement something like Keynote tooltips in a canvas while editing, in mouseDown events, you are out of luck. To remedy that, I created a small class to do it. It tries to imitate as much as possible the Keynote look and feels. -- General/EricForget

See also General/LittleYellowBox.

----

I don't have Keynote. How are Keynote tooltips different from the standard ones?

----

Normal tooltips are for views, but mostly for controls. When the user move the cursor over it, wait a few seconds the tooltip appears for 10 seconds and then disappear. Keynote tooltips looks the same but are there for displaying the position of the moving object, the dimension of the resizing object or the angle for the rotation. Those tooltips appear only during mouseDown:/mouseDragged events. -- General/EricForget

    

----**General/ToolTipTextField Interface**

@interface General/ToolTipTextField : General/NSTextField
@end

----**General/ToolTipTextField Implementation**

@implementation General/ToolTipTextField

- (void) drawRect:(General/NSRect)aRect
{
    [super drawRect:aRect];
    
    General/[[NSColor colorWithCalibratedWhite:0.925 alpha:1.0] set];
    General/NSFrameRect(aRect);
}

@end

----**General/ToolTip Interface**

@interface General/ToolTip : General/NSObject
{
    General/NSWindow                *window;
    General/NSTextField             *textField;
    General/NSDictionary            *textAttributes;
}

+ (void) setString:(General/NSString *)string forEvent:(General/NSEvent *)theEvent;
+ (void) release;

@end

----**General/ToolTip Implementation**

static General/ToolTip	*sharedToolTip = nil;

@interface General/ToolTip (Private)

- (void) setString:(General/NSString *)string forEvent:(General/NSEvent *)theEvent;

@end

+ (void) setString:(General/NSString *)string forEvent:(General/NSEvent *)theEvent
{
    if (sharedToolTip == nil) {
        
        sharedToolTip = General/[[ToolTip alloc] init];
    }
    
    [sharedToolTip setString:string forEvent:theEvent];
}

+ (void) release
{
    [sharedToolTip release];
    sharedToolTip = nil;
}

- (id) init
{
    id        retVal = [super init];
    
    if (retVal != nil) {
        
        // These size are not really import, just the relation between the two...
        General/NSRect        contentRect       = { { 100, 100 }, { 100, 20 } };
        General/NSRect        textFieldFrame    = { { 0, 0 }, { 100, 20 } };
        
        window = General/[[NSWindow alloc]
                    initWithContentRect:    contentRect
                              styleMask:    General/NSBorderlessWindowMask
                                backing:    General/NSBackingStoreBuffered
                                  defer:    YES];
        
        [window setOpaque:NO];
        [window setAlphaValue:0.80];
        [window setBackgroundColor:General/[NSColor colorWithDeviceRed:1.0 green:0.96 blue:0.76 alpha:1.0]];
        [window setHasShadow:YES];
        [window setLevel:General/NSStatusWindowLevel];
        [window setReleasedWhenClosed:YES];
        [window orderFront:nil];
        
        textField = General/[[ToolTipTextField alloc] initWithFrame:textFieldFrame];
        [textField setEditable:NO];
        [textField setSelectable:NO];
        [textField setBezeled:NO];
        [textField setBordered:NO];
        [textField setDrawsBackground:NO];
        [textField setAlignment:General/NSCenterTextAlignment];
        [textField setAutoresizingMask:General/NSViewWidthSizable | General/NSViewHeightSizable];
        [textField setFont:General/[NSFont toolTipsFontOfSize:General/[NSFont systemFontSize]]];
        General/window contentView] addSubview:textField];
        
        [textField setStringValue:@" "]; // Just having at least 1 char to allow the next message...
        textAttributes = [[[textField attributedStringValue] attributesAtIndex:0 effectiveRange:nil] retain];
    }
    
    return retVal;
}

- (void) dealloc
{
    [window release];
    [textAttributes release];
    [super dealloc];
}

- (void) setString:([[NSString *)string forEvent:(General/NSEvent *)theEvent
{
    General/NSSize        size                    = [string sizeWithAttributes:textAttributes];
    General/NSPoint       cursorScreenPosition    = General/theEvent window] convertBaseToScreen:[theEvent locationInWindow;
    
    [textField setStringValue:string];
    [window setFrameTopLeftPoint:General/NSMakePoint(cursorScreenPosition.x + 10, cursorScreenPosition.y + 28)];
    
    [window setContentSize:General/NSMakeSize(size.width + 20, size.height + 1)];
}

----**General/ToolTip Usage Sample**

- (void) mouseDown:(General/NSEvent *)theEvent
{
    General/NSPoint        where = [self convertPoint:[theEvent locationInWindow] fromView:nil];
    General/NSPoint        objectOrigin;
    
    // objectClicked is a object variable
    objectClicked    = [self objectClicked:where];
    
    // Do your mouseDown: job...
    
    
    // Setup Tooltip
    objectOrigin = [objectClicked bounds].origin;
    
    General/[ToolTip
            setString:  General/[NSString stringWithFormat:@"x: %.2f  y: %.2f", objectOrigin.x, objectOrigin.y]
             forEvent:  theEvent];
}

- (void) mouseDragged:(General/NSEvent *)theEvent
{
    General/NSPoint        objectOrigin;
    
    // Do your mouseDragged: job...
    
    
    // Update Tooltip
    objectOrigin = [objectClicked bounds].origin;
    
    General/[ToolTip
            setString:  General/[NSString stringWithFormat:@"x: %.2f  y: %.2f", objectOrigin.x, objectOrigin.y]
             forEvent:  theEvent];
    
    return retVal;
}

- (void) mouseUp:(General/NSEvent *)theEvent
{
    // Do your mouseUp: job...
    
    // Release Tooltip
    General/[ToolTip release];
}



-- General/EricForget

----

----

How 'bout something like this?...

    

#import <General/AppKit/General/AppKit.h>

@interface General/TooltipWindow : General/NSWindow
{
    General/NSTimer *closeTimer;
    id tooltipObject;
}
+ (id)tipWithString:(General/NSString *)tip frame:(General/NSRect)frame display:(BOOL)display;
+ (id)tipWithAttributedString:(General/NSAttributedString *)tip frame:(General/NSRect)frame display:(BOOL)display;

// returns the approximate window size needed to display the tooltip string.
+ (General/NSSize)suggestedSizeForTooltip:(id)tooltip;

// setting and getting the default duration..
+ (void)setDefaultDuration:(General/NSTimeInterval)inSeconds;
+ (General/NSTimeInterval)defaultDuration;

// setting and getting the default bgColor
+ (void)setDefaultBackgroundColor:(General/NSColor *)bgColor;
+ (General/NSColor *)defaultBackgroundColor;

- (id)init;

- (id)tooltip;
- (void)setTooltip:(id)tip;

- (void)orderFrontWithDuration:(General/NSTimeInterval)seconds;

@end


    
#import "General/TooltipWindow.h"

float defaultDuration;
BOOL doneInitialSetup;
General/NSDictionary *textAttributes;
General/NSColor *backgroundColor;

@implementation General/TooltipWindow

+ (void)setDefaultBackgroundColor:(General/NSColor *)bgColor
{
          [backgroundColor release];
    backgroundColor = [bgColor retain];
}
+ (General/NSColor *)defaultBackgroundColor
{
    if (!backgroundColor)
        General/[TooltipWindow setDefaultBackgroundColor: General/[NSColor colorWithDeviceRed:1.0 green:0.96 blue:0.76 alpha:1.0]];

    return backgroundColor;
}

+ (void)setDefaultDuration:(General/NSTimeInterval)inSeconds
{
    doneInitialSetup = YES;
    defaultDuration = inSeconds;
}

+ (General/NSTimeInterval)defaultDuration
{
    return defaultDuration;
}

+ (id)tipWithString:(General/NSString *)tip frame:(General/NSRect)frame display:(BOOL)display
{
    return General/[TooltipWindow tipWithAttributedString:General/[[[NSAttributedString alloc] initWithString:tip] autorelease] frame:frame display:display];
}

+ (id)tipWithAttributedString:(General/NSAttributedString *)tip frame:(General/NSRect)frame display:(BOOL)display
{
    General/TooltipWindow *window = General/[[TooltipWindow alloc] init]; // blank slate
            
    // if the defaultDuration hasn't been set we set it to a default of 5 seconds
    // we like to cache the text attributes used for plain string drawing..
    if (!doneInitialSetup) {
            General/[TooltipWindow setDefaultDuration:5];
            [window setTooltip:@" "]; // Just having at least 1 char to allow the next message...
            textAttributes = General/[[window contentView] attributedStringValue] attributesAtIndex:0 effectiveRange:nil] retain];
    }
    
            [window setTooltip:tip]; // set the tip
            [window setReleasedWhenClosed:display]; // if we display right away we release on close
            [window setFrame:frame display:YES];

        if (display) {
            [window orderFrontWithDuration:[[[TooltipWindow defaultDuration]]; // this is affectively autoreleasing the window after 'defaultDuration'
            return window;
       } else // it's not set to auto-magically go away so make the caller responsible..
            return [window autorelease];

    return nil; // should never get here
}

+ (General/NSSize)suggestedSizeForTooltip:(id)tooltip
{
    General/NSSize tipSize = General/NSZeroSize;

    if ([tooltip isKindOfClass:General/[NSAttributedString class]]) tipSize = [tooltip size];
    else
    if ([tooltip isKindOfClass:General/[NSString class]]) tipSize = [tooltip sizeWithAttributes:textAttributes];
    else
        return tipSize; // we don't know how to get the size of 'tooltip'

        if (!General/NSEqualSizes(tipSize, General/NSZeroSize))
            tipSize.width += 4;

return tipSize;

}


- (id)init
{
    self = [super initWithContentRect:General/NSMakeRect(0,0,0,0)
                              styleMask:General/NSBorderlessWindowMask
                                backing:General/NSBackingStoreBuffered
                                  defer:NO];
     { // window setup...
        [self setAlphaValue:0.90];
        [self setOpaque:NO];
        [self setBackgroundColor:General/[TooltipWindow defaultBackgroundColor]];
        [self setHasShadow:YES];
        [self setLevel:General/NSStatusWindowLevel];
        [self setHidesOnDeactivate:YES];
        [self setIgnoresMouseEvents:YES];
     }

     { // textfield setup...
        General/NSTextField *field = General/[[NSTextField alloc] initWithFrame:General/NSMakeRect(0,0,0,0)];

            [field setEditable:NO];
            [field setSelectable:NO];
            [field setBezeled:NO];
            [field setBordered:NO];
            [field setDrawsBackground:NO];
            [field setAutoresizingMask:General/NSViewWidthSizable | General/NSViewHeightSizable];
            [self setContentView:field];
            [self setFrame:[self frameRectForContentRect:[field frame]] display:NO];
            
                if (!doneInitialSetup) {
                    General/[TooltipWindow setDefaultDuration:5];
                    [field setStringValue:@" "]; // Just having at least 1 char to allow the next message...
                    textAttributes = General/[field attributedStringValue] attributesAtIndex:0 effectiveRange:nil] retain];
                }
            
            [field release];
        }

    return self;
}


- (void)dealloc
{
        if (closeTimer) {
            [closeTimer invalidate];
            [closeTimer release];
        }
    [tooltipObject release];
    [super dealloc];
}

- (id)tooltip { return tooltipObject; }

- (void)setTooltip:(id)tip
{
    id contentView = [self contentView];
    
    [tooltipObject release];
    tooltipObject = [tip retain];
    
    if ([contentView isKindOfClass:[[[NSTextField class]]) {
        if ([tip isKindOfClass:General/[NSString class]]) [contentView setStringValue:tip];
        else
        if ([tip isKindOfClass:General/[NSAttributedString class]]) [contentView setAttributedStringValue:tip];
    }
}

- (void)orderFrontWithDuration:(General/NSTimeInterval)seconds
{
    [super orderFront:nil];
    
    if (closeTimer) { [closeTimer invalidate]; [closeTimer release]; }
    closeTimer = General/[[NSTimer scheduledTimerWithTimeInterval:seconds target:self selector:@selector(close) userInfo:nil repeats:NO] retain];
}


- (General/NSString *)description
{
    return General/[NSString stringWithFormat:@"General/TooltipWindow:\n%@", General/self contentView] stringValue;
}


@end


I stuck a demo project that uses that there General/TooltipWindow class here: http://homepage.mac.com/ryanstevens/.Public/General/TooltipWindowDemo.zip

Maybe someone will find it useful. :)

----

What are the differences between your implementation and the one from Cocoa, outside that you have to do everything by hand? What is the intent of your implementation, which doesn't seems to serves the same needs as the one I provided? --General/EricForget

----
I think my implementation serves the same needs, just in a different way.

I really just wanted more control over the window itself, that and attributed tooltips.

----
I tried out Ryan's version and it works beautifully. Thanks! --Daniel

----
It's been ages since I've looked back at this...wow. I guess the demo link was broken (or was never right in the first place) - that should be fixed now.

Just glad I could contribute back. --General/RyanStevens

----
Thanks for showing me how to create handy little windows; I've wondered how to do this for quite some time, and I'm sure the knowledge will be dangerous in my hands :)

I used Eric's implementation because it was less reading for me to understand.  I like the way that Eric uses class methods to communicate with his singleton, instead of the way Apple does it with stuff like their -sharedDocumentController.  Alot of brain-power and screen real estate is wasted when Apple makes us do:
    
    General/[[NSGobbleDeeGookManager sharedGobbleDeeGookManager] canYouPleaseDoSomething] ;


You can also attach these tool tips to dragged rows of General/NSTableView or General/NSOutlineView.  To do that, referring to Eric's "Tool Tip Usage Sample" above, move his code

*from Eric's mouseDown: to your draggingEntered:
*from Eric's mouseDragged: to your draggingUpdated:
*from Eric's mouseUp: to your draggingExited:, draggingEnded: and acceptDrop:::

The last one goes to three places because there are several ways that a dragging session can end.

 -- Jerry Krinock   2007 Oct 08
