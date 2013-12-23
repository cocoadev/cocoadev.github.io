---
layout: page
title: TransparentScreenSaver
---

Here's the implementation for a screen saver that installs transparent windows. This screen saver does no animation, and is useful for locking the screen with the screen saver system's password feature without actually covering the screen (although I don't know why you'd want that ;-) )

In General/TransparentView.h:
    
#import <General/ScreenSaver/General/ScreenSaver.h>

@interface General/TransparentView : General/ScreenSaverView
{
}
@end


In General/TransparentView.m:
    
#import "General/TransparentView.h"

@implementation General/TransparentView

- (id)initWithFrame:(General/NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
        [self setAnimationTimeInterval:-1];
    }
    return self;
}


- (void)drawRect:(General/NSRect)rect
{
    if (![self isPreview])
        General/self window] setAlphaValue:0.0];
}

@end


Note that we aren't doing any graphics ourselves. Since we aren't calling super in drawRect: the [[ScreenSaverView base class will not draw black. This class disables the animation timer by setting the animation delay to -1. It also makes the view's window transparent if the saver is not running in preview mode. This method is not working with Mac OS X 10.3 (Panther) if password is required to wake from screen saving. See General/ScreenSaverProblemsWhenPasswordRequiredToWakeFromSaverOrSleep.
----
**Questions**
**Q:** Sorry for the stupid question, I have not implemented many screen savers with Cocoa, but can't you just override     - alphaValue returning 0.0 /     -isOpaque returning NO, and override     - drawRect: and do nothing? It just seems better than hitting - setAlphaValue: every time it thinks it needs to draw.

**A:** ?

**Q:** It appears that this no longer works on Mac OS X 10.4 -- instead of showing the desktop beneath the screensaver, it now shows a plain aqua window background (with faint horizontal stripes).  Has anyone succeeded in getting around this?

**A:** You need to 'hole punch' it with clearColor - I call this before the first draw: --General/MathewEis
    
General/self window] setOpaque:NO];
[[self window] setBackgroundColor: [[[NSColor clearColor]];
General/self window] setAlphaValue:0.999f]; // Not sure why the 0.999, but it's in some Apple examples for transparent windows
[[self window] setShowsResizeIndicator:NO];
[self lockFocus];
[[[[NSColor clearColor] set];
General/NSRectFill([self bounds]);
[self unlockFocus];

