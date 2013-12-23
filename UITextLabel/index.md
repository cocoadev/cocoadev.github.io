---
layout: page
title: UITextLabel
---



Subclass of General/UIView. Displays static, uneditable text, with optional shadow, center alignment, wrapping, ellipsis-izing, and so on.

<code>+ (General/GSFont*)defaultFont;

- (void)setText:(General/NSString *)text;

- (void)setFont:(General/GSFont*)font;

- (void)setColor:(General/CGColorRef)color;

- (void)setBackgroundColor:(General/CGColorRef)color;

- (void)setCentersHorizontally:(BOOL)center;

- (void)setWrapsText:(BOOL)wrap;

- (void)setShadowColor:(General/CGColorRef)color;

- (void)setShadowOffset:(General/CGSize)offset;</code>
