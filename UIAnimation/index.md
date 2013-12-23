---
layout: page
title: UIAnimation
---



The following is from http://www.lucasnewman.com/iphone/

**General/UIAnimation**

An abstract superclass used to animate properties of a General/UIView. It is supported by a collection concrete subclasses provided for the alpha, frame, scroll position, affine transformation (including a special subclass for rotation), and zooming.

    
typedef enum {
    kUIAnimationCurveEaseInEaseOut,
    kUIAnimationCurveEaseIn,
    kUIAnimationCurveEaseOut,
    kUIAnimationCurveLinear,
} General/UIAnimationCurve;


// Creating animations

<code>- (id)initWithTarget:(id)target;</code>

// Properties

<code>- (id)target;</code>

<code>- (void)setDelegate:(id)delegate;</code>

<code>- (id)delegate;</code>

<code>- (void)setAction:(SEL)action;</code>

<code>- (SEL)action;</code>

<code>- (void)setAnimationCurve:(General/UIAnimationCurve)animationCurve;</code>

<code>- (float)progressForFraction:(float)fraction;</code>

// Implemented by subclasses

<code>- (void)setProgress:(float)progress;</code>

// Controlling animations

<code>- (void)stopAnimation;</code>

----

**Provided General/UIAnimation subclasses:**

**General/UIAlphaAnimation**

<code>- (void)setStartAlpha:(float)alpha;</code>

<code>- (void)setEndAlpha:(float)alpha;</code>

<code>- (float)alphaForFraction:(float)fraction;</code>

**General/UIFrameAnimation**

<code>- (void)setStartFrame:(General/CGRect)frame;</code>

<code>- (void)setEndFrame:(General/CGRect)frame;</code>

<code>- (General/CGRect)endFrame;</code>

**General/UIRotationAnimation**

<code>- (void)setStartRotationAngle:(float)angle;</code>

<code>- (void)setEndRotationAngle:(float)angle;</code>

**General/UIScrollAnimation**

<code>- (void)setStartPoint:(General/CGPoint)startPoint;</code>

<code>- (void)setEndPoint:(General/CGPoint)endPoint;</code>

**General/UITransformAnimation**

<code>- (void)setStartTransform:(General/CGAffineTransform)transform;</code>

<code>- (void)setEndTransform:(General/CGAffineTransform)transform;</code>

<code>- (General/CGAffineTransform)transformForFraction:(float)fraction;</code>

**General/UIZoomAnimation**

<code>+ (float)defaultDuration;</code>

<code>+ (id)zoomAnimationForTarget:(id)target endScale:(float)scale endScrollPoint:(General/CGPoint)point;</code>

<code>+ (id)zoomAnimationForTarget:(id)target focusRect:(General/CGRect)focusRect deviceBoundaryRect:(General/CGRect)deviceBoundaryRect scale:(float)scale;</code>

<code>- (void)setEndScale:(float)scale;</code>

<code>- (void)setStartScale:(float)scale;</code>

<code>- (void)setEndScrollPoint:(General/CGPoint)point;</code>

<code>- (void)setStartScrollPoint:(General/CGPoint)point;</code>

----

**General/UIAnimator**

General/UIAnimator provides a shared controller for starting and stopping global animations.

<code>+ (id)sharedAnimator;</code>

<code>+ (void)disableAnimation;</code>

<code>+ (void)enableAnimation;</code>

<code>- (void)addAnimation:(General/UIAnimation *)animation withDuration:(double)duration start:(BOOL)start;</code>

<code>- (void)addAnimations:(General/NSArray *)animations withDuration:(double)duration start:(BOOL)start;</code>

<code>- (void)removeAnimationsForTarget:(id)target;</code>

<code>- (void)removeAnimationsForTarget:(id)target ofKind:(Class)classOfTarget;</code>

<code>- (void)startAnimation:(General/UIAnimation *)animation;</code>

<code>- (void)stopAnimation:(General/UIAnimation *)animation;</code>

<code>- (float)fractionForAnimation:(General/UIAnimation *)animation;</code>

----

**General/UIView (Animation)**

This category on General/UIView allows adding General/UIAnimations to the view, and also allows modifying global animation parameters.

<code>+ (void)beginAnimations:(General/NSArray *)animations;</code>

<code>+ (void)endAnimations;</code>

<code>+ (void)enableAnimation;</code>

<code>+ (void)disableAnimation;</code>

<code>+ (void)setAnimationAutoreverses:(BOOL)autoreverses;</code>

<code>+ (void)setAnimationCurve:(General/UIAnimationCurve)animationCurve;</code>

<code>+ (void)setAnimationDelay:(double)startDelay;</code>

<code>+ (void)setAnimationDelegate:(id)delegate;</code>

<code>+ (void)setAnimationDuration:(double)duration;</code>

<code>+ (void)setAnimationFrameInterval:(double)minimumTimeIntervalBetweenFrames;</code>

<code>+ (void)setAnimationFromCurrentState:(BOOL)shouldAnimateFromCurrentState;</code>

<code>+ (void)setAnimationPosition:(General/CGPoint)position;</code>

<code>+ (void)setAnimationStartTime:(double)startTime;</code>

<code>+ (void)setAnimationRepeatCount:(float)repeatCount;</code>

<code>+ (void)setAnimationRoundsToInteger:(BOOL)shouldRoundToInteger;</code>

<code>+ (void)setAnimationWillStartSelector:(SEL)willStartSelector;</code>

<code>+ (void)setAnimationDidStopSelector:(SEL)willEndSelector;</code>

<code>- (void)addAnimation:(General/UIAnimation *)animation forKey:(General/NSString *)key;</code>
