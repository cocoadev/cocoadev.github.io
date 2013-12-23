---
layout: page
title: UIControl
---



Part of the iPhone General/UIKit framework. Subclass of General/UIView.

Analogous to General/NSControl. General/UIControl does not, however, implement setTarget: and setAction: methods like General/NSControl does: It has a single method

  <code>- (void)addTarget:(id)target action:(SEL)action forEvents:(int)eventMask;</code>

which lets one control have multiple targets, and call back in response to different events. The event mask appears to be the same as General/NSEvent's mask enum.

    
        /* from http://ellkro.jot.com/General/WikiHome/iPhoneDevDocs/General/UIControl */
        #define General/UIMouseDown                     1
        #define General/UIMouseDragged                1<<2  //within active area of control
        #define General/UIMouseExitedDragged     1<<3  //move outside active area 
        #define General/UIMouseEntered                  1<<4 //move crossed into active area
        #define General/UIMouseExited                   1<<5 //move crossed out of active area
        #define General/UIMouseUp                   1<<6 //up within the active area
        #define General/UIMouseExitedUp              1<<7 //up outside active area
        /* end from http://ellkro.jot.com/General/WikiHome/iPhoneDevDocs/General/UIControl */

	General/UIValueButton* button = General/[[UIValueButton alloc] initWithFrame:rect];
	
	[button addTarget:self action:@selector(buttonDown:) forEvents:1 /* mouse down */];
	[button addTarget:self action:@selector(buttonUp:) forEvents:(1<<6) /* mouse up */];

