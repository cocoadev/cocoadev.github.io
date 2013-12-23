---
layout: page
title: NSViewFade
---

There are a couple of tips on the site for dealing with fade-in/out effects for windows, but the trick seems to be a bit more difficult for NSView's.  FadeAViewAndItsContents has one option, but it requires creating a helper NSWindow object to hold the NSView in question.  In my particular app, I want to fade a view in and out of visibility when the app is in full screen mode (think: QuickTime's controller when you're watching a movie full screen).  Using a helper NSWindow wasn't an option since only one window can appear at the CGShieldingWindowLevel().  The helper wouldn't be visible.

So I came up with a little category on NSView that does the trick nicely, albeit in Tiger only.  The code fails gracefully in my build environment since I build separate Panther and Tiger versions for other reasons.  It should be possible to add runtime checks to make the code work in a unified build for both OS's, but I haven't added it at this point.  Any additions would of course be welcome.

NSView+Fade.h:
    
#import <Cocoa/Cocoa.h>


@interface NSView(Fade)
- (IBAction)setHidden:(BOOL)hidden withFade:(BOOL)fade;
@end


NSView+Fade.m:
    
#import "NSView+Fade.h"

/**
	A category on NSView that allows fade in/out on setHidden:
 */
@implementation NSView(Fade)
/**
	Hides or unhides an NSView, making it fade in or our of existance.
 @param hidden YES to hide, NO to show
 @param fade if NO, just setHidden normally.
*/
- (IBAction)setHidden:(BOOL)hidden withFade:(BOOL)fade {
	if(!fade) {
		// The easy way out.  Nothing to do here...
		[self setHidden:hidden];
	} else {
// FIXME: It would be better to check for the availability of NSViewAnimation at runtime intead
// of at compile time.  I'm lazy, and I make two builds anyways, so I do it at compile. -ZSB
#if MAC_OS_X_VERSION_MIN_REQUIRED >= MAC_OS_X_VERSION_10_4
		// We're building for (at least) Tiger, so we can use NSViewAnimation
		if(!hidden) {
			// If we're unhiding, make sure we queue an unhide before the animation
			[self setHidden:NO];
		}
		NSMutableDictionary *animDict = [NSMutableDictionary dictionaryWithCapacity:2];
		[animDict setObject:self forKey:NSViewAnimationTargetKey];
		[animDict setObject:(hidden ? NSViewAnimationFadeOutEffect : NSViewAnimationFadeInEffect) forKey:NSViewAnimationEffectKey];
		NSViewAnimation *anim = [[NSViewAnimation alloc] initWithViewAnimations:[NSArray arrayWithObject:animDict]];
		[anim setDuration:0.5];
		[anim startAnimation];
		[anim autorelease];
#else
		// We're building for Panther, so just do a normal hide
		[self setHidden:hidden];
#endif
	}
	
}
@end


The above code is placed in the public domain if anyone cares to use it.

- ZacBedell

----
See also AMViewAnimation. --!Andy :)
----

