---
layout: page
title: UITransitionView
---



The following describe the direction the content moves--that is, the opposite direction of the viewport. When transitioning into a submenu, use UIT<nowiki/>ransitionShiftLeft; when navigating back, use UIT<nowiki/>ransitionShiftRight.

    
typedef enum
{
  General/UITransitionShiftImmediate = 0, // actually, zero or anything > 9
  General/UITransitionShiftLeft = 1,
  General/UITransitionShiftRight = 2,
  General/UITransitionShiftUp = 3,
  General/UITransitionFade = 6,
  General/UITransitionShiftDown = 7
} General/UITransitionStyle;


<code>- (void)transition:(UIT<nowiki/>ransitionStyle)style toView:(General/UIView*)view;</code>

Animate only the to view. Will look like a "slide over".

<code>- (void)transition:(UIT<nowiki/>ransitionStyle)style fromView:(General/UIView*)view toView:(General/UIView*)view;</code>

Animate both views. Will look like a "swap".

<code>- (void)setDelegate:(id)delegate;</code>

----

**Delegate methods**

<code>- (float)durationForTransition:(General/UITransitionView*)view;</code>

<code>- (void)transitionViewDidStart:(General/UITransitionView*)view;</code>

<code>- (void)transitionViewDidComplete:(General/UITransitionView*)view fromView:(General/UIView*)from toView:(General/UIView*)to;</code>

<code>- (void)transitionViewDidComplete:(General/UITransitionView*)view finished:(BOOL)flag;</code>

<code>- (void)transitionViewDidComplete:(General/UITransitionView*)view;</code>

----

I found a more complete version on a Japanese site (http://son-son.sakura.ne.jp/programming/iphone_uitransitionview.html). Now I can't read Japanese, but they do seem to work!

    
typedef enum
{
  General/UITransitionNothing = 0,
  General/UITransitionBothShiftLeft = 1,
  General/UITransitionBothShiftRight = 2,
  General/UITransitionBothShiftUp = 3,
  UITransitionError4 = 4,
  General/UITransitionCurrentShiftDown = 5,
  UITransitionError6 = 6,
  General/UITransitionBothShiftDown = 7,
  General/UITransitionNextShiftUp = 8,
  General/UITransitionNextShiftDown = 9
} General/UITransitionStyle;

