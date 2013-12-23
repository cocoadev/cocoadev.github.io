---
layout: page
title: AMViewAnimation
---

Today, I discovered the magic of NSViewAnimation. But I found it rather awkward to use on a quick basis, so I cooked up a lovely category on NSView that allows you to send it messages like fadeOut and animateToFrame in about 1/10th the characters. Sensible defaults are provided for everything, but you can change them if necessary. MIT License.


**NSView+AMAnimationAdditions.h**
    
 //
 //  NSView+AMAnimationAdditions.h
 //  MovingView
 //
 //  Created by Andy Matuschak on 11/6/05.
 //  Copyright 2005 Andy Matuschak. All rights reserved.
 //
 
 #import <Cocoa/Cocoa.h>
 
 @interface NSView (AMAnimationAdditions)
 - (IBAction)fadeOut:sender;
 - (IBAction)fadeIn:sender;
 - (void)animateToFrame:(NSRect)rect;
 - (void)fadeToFrame:(NSRect)rect; // animates to supplied frame; fades in if view is hidden; fades out if view is visible
 
 + (void)setDefaultDuration:(NSTimeInterval)duration;
 + (void)setDefaultBlockingMode:(NSAnimationBlockingMode)mode;
 + (void)setDefaultAnimationCurve:(NSAnimationCurve)curve;
 @end


**NSView+AMAnimationAdditions.m**
    
 //
 //  NSView+AMAnimationAdditions.m
 //  MovingView
 //
 //  Created by Andy Matuschak on 11/6/05.
 //  Copyright 2005 Andy Matuschak. All rights reserved.
 //
 
 #import "NSView+AMAnimationAdditions.h"
 
 NSTimeInterval AMDefaultAnimationDuration = -1; // -1 makes the system provide a default duration
 NSAnimationBlockingMode AMDefaultAnimationBlockingMode = NSAnimationNonblocking;
 NSAnimationCurve AMDefaultAnimationCurve = NSAnimationEaseInOut;
 
 @implementation NSView (AMAnimationAdditions)
 
 - (NSArray *)animationArrayForParameters:(NSDictionary *)params
 {
 	NSMutableDictionary *animationDetails = [NSMutableDictionary dictionaryWithDictionary:params];
 	[animationDetails setObject:self forKey:NSViewAnimationTargetKey];
 	return [NSArray arrayWithObject:animationDetails];
 }
 
 - (void)playAnimationWithParameters:(NSDictionary *)params
 {
 	NSViewAnimation *animation = NSViewAnimation alloc] initWithViewAnimations:[self animationArrayForParameters:params;
 	[animation setAnimationBlockingMode:AMDefaultAnimationBlockingMode];
 	[animation setDuration:AMDefaultAnimationDuration];
 	[animation setAnimationCurve:AMDefaultAnimationCurve];
 	[animation setDelegate:self];
 	[animation startAnimation];
 }
 
 - (void)animationDidEnd:(NSAnimation *)animation
 {
 	[animation release];
 }
 
 - (void)fadeWithEffect:effect
 {
 	[self playAnimationWithParameters:[NSDictionary dictionaryWithObject:effect forKey:NSViewAnimationEffectKey]];
 }
 
 - (IBAction)fadeOut:sender
 {
 	[self fadeWithEffect:NSViewAnimationFadeOutEffect];
 }
 
 - (IBAction)fadeIn:sender
 {
 	[self fadeWithEffect:NSViewAnimationFadeInEffect];
 }
 
 - (void)animateToFrame:(NSRect)newFrame
 {
 	[self playAnimationWithParameters:[NSDictionary dictionaryWithObject:[NSValue valueWithRect:newFrame] forKey:NSViewAnimationEndFrameKey]];
 }
 
 - (void)fadeToFrame:(NSRect)newFrame
 {
 	[self playAnimationWithParameters:[NSDictionary dictionaryWithObjectsAndKeys:[NSValue valueWithRect:newFrame], NSViewAnimationEndFrameKey, [self isHidden] ? NSViewAnimationFadeInEffect : NSViewAnimationFadeOutEffect, NSViewAnimationEffectKey, nil]];
 }
 
 + (void)setDefaultDuration:(NSTimeInterval)duration
 {
 	AMDefaultAnimationDuration = duration;
 }
 
 + (void)setDefaultBlockingMode:(NSAnimationBlockingMode)mode
 {
 	AMDefaultAnimationBlockingMode = mode;
 }
 
 + (void)setDefaultAnimationCurve:(NSAnimationCurve)curve
 {
 	AMDefaultAnimationCurve = curve;
 }
 
 @end


Requires 10.4, of course. I hope these things prove useful. Please let me know if there are problems or suggestions.

-- AndyMatuschak


Category:CocoaDevUsersAdditions

