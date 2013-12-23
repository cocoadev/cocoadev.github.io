---
layout: page
title: BasicBlackScreenSaver
---

Here's the implementation for a screen saver that blanks the screen and does no animation. It's kind of goofy now that we have energy saver, but some people insist on minimalism ... 

In BlackView.h:
    
#import <ScreenSaver/ScreenSaver.h>

@interface BlackView : ScreenSaverView
{
}
@end


In BlackView.m:
    
#import "BlackView.h"

@implementation BlackView

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
        [self setAnimationTimeInterval:-1];
    }
    return self;
}

@end


Note that we aren't doing any graphics ourselves. The ScreenSaverView base class will draw black. The only thing we're doing is disabling the animation timers by setting the animation time to -1.

