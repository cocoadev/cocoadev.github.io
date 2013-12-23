---
layout: page
title: AffineMess
---




Questions about animating NSBezierPath through NSAffineTransform with NSTimer

I am experimenting with NSAffineTransform, specifically trying to rotate an NSBezierPath around its own center.

What is necessary is to draw the object at the origin, do the rotation and translate it out to the desired position.

Here is my custom view class.

    
 #import <AppKit/AppKit.h>
 
 @interface UFOView: NSView
 {
 	float fps;
         float x0, y0;
 	
 	NSAffineTransform *rotr;              // rotate each fram by 5 degrees
 	NSAffineTransform *disptr;           // translate path from origin to center of view
 	NSBezierPath *path;
 	NSBezierPath *drawPath;
 }
 
 @end


    
 #import "UFOView.h"
 
 @implementation UFOView
 
 - ( id ) initWithFrame: ( NSRect ) frame
 {
 	NSTimer *timer;
 	fps = 1. / 24.;
 	
 	self = [ super initWithFrame: frame ];
 	if ( self )
 	{
 		timer = [ NSTimer scheduledTimerWithTimeInterval: fps target: self
 			selector: @selector( singleStep: ) userInfo: nil repeats: YES ];
 		rotr = [ [ NSAffineTransform transform ] retain ];
 		disptr = [ [ NSAffineTransform transform ] retain ];
 		
 		path = [ [ NSBezierPath bezierPath ] retain ];
 	}
 	return self;
 }
 
 - ( void ) dealloc
 {
 	[ path release ];
 	if ( drawPath ) [ drawPath release ];
 	
 	[ rotr release ];
 	[ disptr release ];
 	[ super dealloc ];
 }
 
 - ( void ) awakeFromNib
 {
 	x0 = [ self bounds ].size.width * 0.5;
 	y0 = [ self bounds ].size.height * 0.5;
 	[ disptr translateXBy: x0 yBy: y0 ];
 	
 	[ path moveToPoint: NSMakePoint( -22, -30 ) ];
 	[ path lineToPoint: NSMakePoint( -2, 30 ) ];
 	[ path lineToPoint: NSMakePoint( 28, -30 ) ];
 	[ path lineToPoint: NSMakePoint( -35, 8 ) ];
 	[ path lineToPoint: NSMakePoint( 35, 8 ) ];
 	[ path closePath ];
 }
 
 - ( void ) drawRect: ( NSRect ) rect
 {
 	[ [ NSColor lightGrayColor ] set ];
 	NSRectFill( [ self bounds ] );
 
 	[ drawPath fill ];
 	[ [ NSColor blackColor ] set ];
 	[ drawPath stroke ];
  
 
 - ( void ) singleStep: ( NSTimer * ) t
 {
 	NSAffineTransform *aftr = [ NSAffineTransform transform ];
         
         [ drawPath release ];
 }       drawPath = [ path copy ];
 	
 	[ rotr rotateByDegrees: 5.0 ];
 	[ aftr appendTransform: rotr ];
 	[ aftr appendTransform: disptr ];
 	[ drawPath transformUsingAffineTransform: aftr ];
 	[ self setNeedsDisplay: YES ];
 }
 
 @end


Great title to the page ... but are you asking a question? You might want to cache the path and only update it when the bounds change... likewise calculate and cache the local centre of the path to use in your transformation. Then it's just a matter of remembering what order to apply the translations ... 

----

I am definitely asking questions, but they may need to go in a topic whose title focuses more on the problem - which now is the animation.
I have it working now using an NSTimer to do the animation.
Previous memory allocation problems that progressively slowed the animation were not detectable in ObjectAlloc because
it was manufacturing NSPoint structs in the drawRect method. Fixed that!
I don't think the thing is leaking memory, now -- I have run it through Object Alloc. Do I need to explore multithreading now? (Yikes!)

----
Manufacturing NSPoints? They're either statically or automatically allocated, so you won't be leaking memory unless you were allocating memory for them with something in the malloc(3) family. --BenStiglitz

----

Fixed the crashing-on-resize problem by initializing the displacement transform in awakeFromNib. This falls down a little
when the view is resized because the bounds are updated. This is a little tougher to fix, which I suppose I could do by caching the
old bounds, translating back by the old bounds, and translating by the new bounds. A better way is probably to use notifications or
a delegate method when the window resizes - I guess I could make my custom view the window's delegate.

To put a humorous spin on it, this all feels a little like learning how to whistle... perhaps such learning is best done out of earshot of others?

*Why? Anyone else with the same questions or going through the same process would find it immensely helpful. Whistle away! Just tape up all glass to keep the shards from scattering ...*

----

To be helpful I'll offer a robust and tidier (hopefully) solution -- RbrtPntn
    
 #import <Cocoa/Cocoa.h>
 @interface UFOView : NSView {
     float angle;
     NSBezierPath *path; 
 }
 @end
 
 //helper - gives center of rectangle
 static NSPoint MidRect(NSRect rect) { return NSMakePoint(NSMidX(rect), NSMidY(rect)); }
 
 @implementation UFOView
 - (void)awakeFromNib {
     path = [[NSBezierPath bezierPath] retain]; //cache to save recreating
     [path moveToPoint:NSMakePoint(-22, -30)];
     [path lineToPoint:NSMakePoint(-2, 30)];
     [path lineToPoint:NSMakePoint(28, -30)];
     [path lineToPoint:NSMakePoint(-35, 8)];
     [path lineToPoint:NSMakePoint(35, 8)];
     [path closePath];
     [NSTimer scheduledTimerWithTimeInterval:1.0/24 target:self selector:@selector(singleStep:) userInfo:nil repeats:YES];
 }
 - (void)dealloc {
     [path release];
     [super dealloc];
 }
 - (void)singleStep:(NSTimer *)timer {
     angle += 5.0;
     [self setNeedsDisplay:YES];
 }
 - (void)drawRect:(NSRect)rect { // unless you specify opaque it clears the view for you
     NSPoint pathCenter  = MidRect([path bounds]); // could cache this too...
     NSPoint viewCenter = MidRect([self bounds]);
     
     NSAffineTransform *transform = [NSAffineTransform transform]; // it will autorelease
     [transform translateXBy:viewCenter.x yBy:viewCenter.y];
     [transform rotateByDegrees:angle];
     [transform translateXBy:-pathCenter.x yBy:-pathCenter.y];
  
     // alternatively to creating a new path is to [transform concat] and just draw...
     NSBezierPath *drawPath = [transform transformBezierPath:path]; // it will autorelease
     [[NSColor lightGrayColor] setFill];
     [[NSColor blackColor] setStroke];
     [drawPath fill];
     [drawPath stroke];
 }
 @end

