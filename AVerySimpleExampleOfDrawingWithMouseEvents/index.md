---
layout: page
title: AVerySimpleExampleOfDrawingWithMouseEvents
---



Good evening everyone. I am recently getting into the world of drawing with Cocoa. I've programmed a few easy apps just for fun, but never drawn anything myself - always used InterfaceBuilder for that.

I have spent a long while trying to find good, easy examples of how to draw things and respond to user input to modify those drawings. http://cocoadevcentral.com/ has been a lot of help getting started, but I wanted something a bit more. I know this all seems very simple to most Cocoa pros, but all the talk of NSViews, custom views, First Responders, etc. had (and still partially has) me confused.

So I thought I would share this very simple code that does something neat. The screen starts with a blue box in the corner of the screen. When you click somewhere, the box moves there. So simple, but so difficult to anyone who is new to Cocoa/Objective-C.

This all assumes that you've been through both Drawing Tutorials at http://cocoadevcentral.com/ and have everything set up in terms of the content view, etc.
    
//
//  MyView.h
// 


#import <Cocoa/Cocoa.h>


@interface MyView : NSView {

	NSPoint mouseLocation;
}

@end




    
//
//  MyView.m
//

#import "MyView.h"


@implementation MyView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code here.
		
	//this will cause the box to be drawn at the content view's origin when drawRect: is first called
	mouseLocation = NSMakePoint ( 0,0 );

    }
    return self;
}

-(void)mouseDown:(NSEvent *)theEvent{
	//get loc of mouse, convert it to views coordinates, store it in instance variable.
	mouseLocation  =  [self convertPoint:[theEvent locationInWindow] fromView:nil];
	//this is needed to let the window know to re-draw the content.
	[self setNeedsDisplay:YES];

}


- (void)drawRect:(NSRect)rect {

	//most of this should look familiar from http://cocoadevcentral.com/ 

	NSRect blueRect;	
	//this next line of code makes the blue box appear at your mouse when drawn!
	blueRect.origin = NSMakePoint( mouseLocation.x, mouseLocation.y ); 

	blueRect.size.width = [self bounds].size.width/20.0;
	blueRect.size.height = blueRect.size.width;
	
	NSBezierPath * bluePath;
	bluePath = [NSBezierPath bezierPathWithRect:blueRect];
	
	[[NSColor blueColor] set];
	[bluePath fill];
}



//put this in so your view can respond to events.
-(BOOL) acceptsFirstResponder {return YES;}

@end




----

Try NSLog'ing in the mouseEvents. I think you need to 'tell' the window the view's in (call [self window]) - (void)setAcceptsMouseMovedEvents:(BOOL)flag...

So it should be something like this:

    
- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code here.
		
	//this will cause the box to be drawn at the content view's origin when drawRect: is first called
	mouseLocation = NSMakePoint ( 0,0 );
       if ([self window]!=nil){
             self window] setAcceptsMouseMovedEvents:YES];
       }
     
    }
    return self;
}


-- Charlie
----
[self window] will almost certainly return nil in a view's -initWithFrame: method because the view can't be added to a window until after it is initialized.

-awakeFromNib would be a choice for calling [[self window] setAcceptsMouseMovedEvents:YES], but other methods like -becomeFirstResponder may be more appropriate.

Also, the reply above does not seem related to the prior text.  Is the reply above needed ?

----

I'd say not. You only need mouse moved events if you're interested in the mouse moving regardless of whether its button is pressed. That isn't relevant to this example, which is meant to be as simple as possible. It might be more useful to show how to drag the shape - it requires exactly two lines of code (to convert the point and set 'mouseLocation') in the -mouseDragged: method. --[[GrahamCox

