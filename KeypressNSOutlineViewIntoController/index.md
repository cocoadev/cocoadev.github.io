---
layout: page
title: KeypressNSOutlineViewIntoController
---

I have been struggling for some time to find how to get a keypress in NSOutlineView into my Controller.

There seem to be many others asking similar questions so I decided document my solution which may help.

I created a class M<nowiki/>yOutlineView  subclassing NSOutlineView
and implemented @protocol M<nowiki/>yOutlineViewDelegate


M<nowiki/>yOutlineView overrides keyDown: and sends any key to the delegate.

If the delegate handles the key it should return YES, otherwise NO.

    
//  MyOutlineView.h
#import <Cocoa/Cocoa.h>

@protocol MyOutlineViewDelegate
@optional
-(BOOL) keyPressedInOutlineView:(unichar) character;
@end

@interface MyOutlineView : NSOutlineView {
	NSObject <MyOutlineViewDelegate> *keyDelegate;
}
@property (assign) IBOutlet NSObject <MyOutlineViewDelegate> *keyDelegate;

@end

    
//  MyOutlineView.m
#import "MyOutlineView.h"

@implementation MyOutlineView
@synthesize keyDelegate;

-(void)keyDown:(NSEvent *)theEvent
{
	unichar character = theEvent characters] characterAtIndex:0];
	if([self.keyDelegate respondsToSelector:@selector(keyPressedInOutlineView:)])
		if([self.keyDelegate keyPressedInOutlineView:character])
		return;
	[super keyDown:theEvent];
}

@end

In Interface Builder change the class of the O<nowiki/>utlineView to M<nowiki/>yOutlineView and connect the keyDelegate output to the Controller. 
Implement the following in your Controller and process the keys as required.

    
-(BOOL)keyPressedInOutlineView:(unichar) character {
	[[NSLog (@"Key in Controller! %C", character);
	if (character == 0x0d) {
        NSLog (@"Return in OutlineView!");
		return YES;
	}
	return NO;
}

