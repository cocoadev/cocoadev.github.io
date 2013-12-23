---
layout: page
title: KissyFaceView
---

KissyFaceView is some CategorySampleCode that demonstrates using ClassCategories to declare "private" method API within a .m file to keep it separate from the public .h file. The compiler does not enforce any notion of  public/private methods. "private" here means someone simply browsing your .h file won't know about the existence of the private method. Keep your source clean!

KissyFaceView implements a simple, clean public interface:

    
// KissyFaceView.h

#import <AppKit/AppKit.h>

@interface KissyFaceView : NSView
{
    NSMutableArray *lipImages;
    NSTimer *animationTimer;
}
- (void)setAnimationRate:(NSTimeInterval)rate;
@end


To use the object one just instantiates the view and installs it a  window. The class will do the rest for you: draw smooching lips floating around in the view. Perhaps you'll use this code in your app's splash-screen and some other places. KissyFaceView's animation timer will call a call back method a few times a second to drive the animation. We'll keep the details of this call back method out of the public API, and instead declare it in our .m file:



    
// KissyFaceView.m

#import "KissyFaceView.h"

@interface KissyFaceView (PrivateMethods)
- (void)KFV_animate:(NSTimer*)timer;
@end

@implementation
[ ... ]


The class will set up an animation timer to fire every so often. The timer will call the KFV_animate: method. Clients shouldn't call _animate: directly, so you don't want to clutter your private API with it. By declaring the method ahead of time, you can refer to the method in your code w/o triggering any compile warnings. 

Note that identifiers must not begin with an underscore: all such are reserved by Apple and GCC. The suggestion made in the CodingGuidelines is to use the initials of your project or class, as above.

The remainder of KissyFaceView is left as an exercise for the reader. Best of luck! 

:-*

-- MikeTrent

