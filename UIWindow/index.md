---
layout: page
title: UIWindow
---



Part of the iPhone General/UIKit framework. Subclass of General/UIView.

<code>- (id)initWithContentRect:(General/CGRect)rect;</code>

<code>- (void)orderFront:(id)sender;</code>

<code>- (void)makeKey:(id)sender;</code>

<code>- (void)_setHidden:(BOOL)flag;</code>

<code>- (void)setContentView:(General/UIView*)view;</code>

// setting the backlight level

<code>- (void)setLevel:(float)level;</code>

<code>- (float)level;</code>

// handling interface events - ??

<code>- (BOOL)shouldRespondToStatusBarHeightChange;</code>

<code>- (void)handleStatusBarHeightChange;</code>
