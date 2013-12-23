---
layout: page
title: NSParagraphStyle
---

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSParagraphStyle_Class/index.html]

NSParagraphStyle and its subclass NSMutableParagraphStyle encapsulate the paragraph or ruler attributes used by the NSAttributedString classes.

----

To get a usable mutable paragraph style, you have to create a mutable copy of the NSParagraphStyle object returned by     [NSParagraphStyle defaultParagraphStyle]. Then make your changes to that object and put it in your string's attributes.

e.g. to get centered text:

    
NSMutableParagraphStyle *centeredStyle = [[NSParagraphStyle defaultParagraphStyle] mutableCopy];
[centeredStyle setAlignment:NSCenterTextAlignment];

NSDictionary *attrs = [NSDictionary dictionaryWithObjectsAndKeys:centeredStyle copy] autorelease], [[NSParagraphStyleAttributeName, nil];

[centeredStyle release];

NSAttributedString *centeredString = [[NSAttributedString alloc] initWithString:@"This is\ncentered" attributes:attrs];


