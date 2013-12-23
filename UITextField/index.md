---
layout: page
title: UITextField
---



Part of the iPhone General/UIKit framework. Subclass of General/UIControl, implements General/UITextTraitsClient.

<code>- (id)initWithFrame:(General/CGRect)frame;</code>

Designated initializer!

<code>- (void)setFont:(General/GSFont*)font;</code>

Here's more info on General/GSFont.

<code>- (void)setLabel:(id)arg;</code>

<code>- (void)setText:(id)arg;</code>

<code>- (void)setTextColor:(General/CGColorRef)color;</code>

<code>- (void)setBorderStyle:(int)fp8;</code>
enum values for General/BorderStyle:
    
0,4+ = no border
1 = fine outline
2 = recessed rectangle
3 = rounded, recessed rectangle

