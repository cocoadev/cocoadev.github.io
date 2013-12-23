---
layout: page
title: CoreAnimation
---



**Core Animation** is a new technology in Mac OS X 10.5 that provides facilities for animation.  It provides for a hierarchy of layers (2D surfaces) -- each with its own 3D transformation matrix -- as well as control over the compositing process that renders one layer on top of another.  Core Animation provides a timer facility to actually make time-dependent animations.

In AppKit, the easiest way to use Core Animation through the "layer-backed mode" and "animator proxies".  In layer-backed mode, an entire hierarchy of subviews is is rendered into a parallel hierarchy of CALayers. If desired, you can get to the layer that backs a particular view by calling     -[NSView layer] and you can even provide your own layer for a view's backing with     -[NSView setLayer:layer]. This is rarely necessary though, as you get a significant amount of power from AppKit without any additional modification of views.

Animator proxies can be used with or without layer-backed mode. This means that animator proxies aren't really a Core Animation-specific feature at all. However, you will likely see notable improvements in performance in layer-backed mode, and some animations don't work as well when using animators without layer-backed mode. You get an animator proxy by sending     -[NSView animator].  Like all proxies, you can interact with it as if it were the view, and can call any methods on it you wish. This proxy knows how to animate certain things, like change in position, size, or visibility.  If a method modifies an animatable property like frame or alphaValue or rotation, it intercepts the message and performs the animation implicitly on your behalf. Any other messages are immediately forwarded to the view class. You can also customize how the proxy animates modifications made to specific animatable properties, and you can even define your own animatable properties.

For example, to move a view to a new position, you would do:

    
[view setFrame:newFrame];


To animate it using CoreAnimation instead, simply do this:

    
view animator] setFrame:newFrame];


For more: https://developer.apple.com/leopard/devcenter/docs/documentation/[[GraphicsImaging/Conceptual/Animation_Overview/index.html

----
Could someone post a step by step HOWTO make window disappear with animation (dissolve) after sending Close message? 
----
I'm still learning CoreAnimation but here's my step by step solution. Please modify it if you find a way to make it even more simple.

1. Create new Cocoa Application project in XCode. Add QuartzCore.Framework to Linked Frameworks.

2. Create new Cocoa Objective-C NSWindowController subclass named FadeWindowController. Edit the implementation so it looks like this:

    
#import <QuartzCore/QuartzCore.h>
#import "FadeWindowController.h"

@implementation FadeWindowController
- (IBAction)showWindow:(id)sender
{
    // If the window was hidden, animate its alpha value so it fades in.
    if (![self.window isVisible])
    {
        self.window.alphaValue = 0.0;
        [self.window.animator setAlphaValue:1.0];
    }
    [super showWindow:sender];
}

- (BOOL)windowShouldClose:(id)window
{
    // Animate the window's alpha value so it fades out.
    [self.window.animator setAlphaValue:0.0];
     // Don't close the window immediately so we can see the animation.
    return NO;
}

- (void)awakeFromNib 
{
    CAAnimation *anim = [CABasicAnimation animation];
    [anim setDelegate:self];
    [self.window setAnimations:[NSDictionary dictionaryWithObject:anim forKey:@"alphaValue"]];
}


- (void)animationDidStop:(CAAnimation *)animation finished:(BOOL)flag 
{
    if(self.window.alphaValue == 0.00) [self close]; //detect end of fade out and close the window
}

@end


3. Open MainMenu.nib in Interface Builder. Create new NSObject and set its class to FadeWindowController in Object Identity inspector.

4. Connect FadeWindowController's window outlet to the Window.

5. Connect the Window's delegate outlet to FadeWindowController.

6. Create "Fade Window" menu item to Window menu. Connect its sent action to showWindow: action of FadeWindowController.

7. Build and run. The window should fade out and back in when you open it with the menu item.

BTW, is there any guidelines or consensus about the proper usage of the property syntax? Should I use     self.window.alphaValue = 0.0,     [self window].alphaValue = 0.0 or     self window] setAlphaValue:0.0]? I'd prefer to use less square brackets and more of the dot syntax.
----

Did you try     -[[myWindow animator] performClose:self]?  Reading the documentation for [[NSAnimatablePropertyContainer (the protocol which provides     -[NSView animator]): *"The animator proxy object should be treated as if it was the receiver itself, and may be passed to any code that accepts the receiver as a parameter."*

Tweaked code and added animation delegate -- RbrtPntn (I'm learning this too - need more example code!)

Example Code:
 
* http://developer.apple.com/samplecode/CocoaSlides/index.html - BUT, code refers to earlier version of API, so use common sense to rename the 'LK' errors to 'CA' equivalents.
* file:///Developer/Examples/Quartz/Core\ Animation/ - CovertFlow and Recipes
* file:///Developer/Examples/AppKit/AnimatingViews/


----

I'm still confused as to why you're not using     myWindow animator] orderOut:] or     [[myWindow animator] performClose:].

----
They just hide/close the window without animation. Another quote from [[NSAnimatablePropertyContainer documentation: *An object's automatically animated properties are those for which animationForKey: finds and returns an CAAnimation instead of nil, often because defaultAnimationForKey: specifies a default animation for the key.*. orderOut: and performClose: just perform the standard action through the animator proxy object.

----
I'm using CoreAnimation to animate the movement of a CALayer (    selection in the following code).

Using     selection.position = to; does the right thing, but I now want to add a delegate to catch the end of the animation. One solution is:
    
  CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"position"];
  anim.delegate = self; //to get the animationDidStop:finished: message
  anim.fromValue = [NSValue valueWithPoint:NSPointFromCGPoint(selection.position)];
  anim.toValue = [NSValue valueWithPoint:NSPointFromCGPoint(to)];
  [selection addAnimation:anim forKey:@"positionAnimation"];

which also works well - BUT once the animation ends, the position of     selection jumps back to the initial starting position. Why does     selection not stay at the finishing position?

----
You need to set the animation's fillMode = kCAFillModeForwards and set removedOnCompletion = NO to make the animation continue to apply on the presentation layer at the end of the animation duration. You could then remove the animation and set the final position property from the animation's delegate.

A better solution would be to just set the position property directly on the layer, and then use the actionForLayer:forKey: delegate method in the layer's delegate to return a CABasicAnimation on the "position" event when you see it (leave the toValue and fromValue blank), and it will animate the property automatically.

----

Continuing with the experimentation - now into 3D. I've added the following perspective transform (from the apple docs) to my root CALayer:
    
  CATransform3D t = CATransform3DIdentity;
  t.m34 = 1.0 / -850;
  rootLayer.transform = t;

This works nicely and I can now see clearly the effect of animating zPosition on the child layers -  however, the child layers now have increasing tendency to leave 'trails', i.e. they don't automatically clear/redraw enough of the view (also seen this occasionally in 2d cases when using shadows) - is this a bug and/or can I do anything trivial to avoid this?  

----

This is a known bug I have open with Apple. As of 10.5.1 there is still no fix. In my case, it appeared when I added a compositing filter to the layer.

----

I know that this is a little off topic, but if anyone else watched monday's (11/5/2007) episode of "Chuck", who else thinks that they my have used Leopard an CoreAnimation to animate those images in the opening scene?

----

I saw it, too. It's possible they did.

----

That's _totally_ what I thought when I saw it!

----

Hi, I noticed something odd about Core Animation:
I was trying to make an NSTableView fade out using tv animator] setAlphaValue:0.0] which is apparently unlikely to work as [[NSTableView doesn�t conform to the NSAnimatablePropertyContainer Protocol� If anyone has an easy way of doing this, let me know!

So, doing hundreds of tests to make it work, I found that:
 
*Embedding the TV in a customView and setting this custom view to want layer **in IB**,
*Telling this same custom view     [customView setLayer:[CALayer layer]];
*Telling the TV     [tv setHidden:YES];


makes the TV fade out (this is actually documented, and seems to be independent from setting or not a transition for subviews in IB�). This is the exact and only way to make it (AFAICT).

My questions are: 
 
*Why do I have to provide a layer, isn�t it supposed to be done for me if I check the box in IB?
*Why can�t I let the box in IB unchecked and write     [customView setWantsLayer:YES];


Thanks for any clue!

Flofl.

----

CoreAnimation Sample Code: ArtGallery 1.0.1  http://theocacao.com/document.page/528

----

More CoreAnimation Sample Code, explaining additive animation, while supplies last:
http://www.sendspace.com/file/37kork
In a transaction with actions disabled, set the new value with the dot syntax. Then create a CABasic animation with timing function control points .5,0,.5,1. The toValue is NSZeroPoint, NSZeroRect, or the identity transform. the fromValue is the old model value minus the new value. For transforms you can concatenate the old value with the inverse of the new. Add the animation to the layer with a nil key. The effect is beautifully curved animation with potentially many running at the same time. It is the best way to respond to user actions like window resize or mouse events.

