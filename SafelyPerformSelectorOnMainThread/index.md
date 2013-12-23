---
layout: page
title: SafelyPerformSelectorOnMainThread
---

I had a problem where I was using performSelectorOnMainThread:... to update UI but was getting exceptions when an update occurred whilst the mouse was down for a user. Using the modes parameter of performSelectorOnMainThread:... doesn't work since even though NSTextView's mouseDown function runs the event loop in NSEventTrackingRunLoopMode, the queuePeriodicEvent framework function gets called which runs the loop in NSDefaultRunLoopMode.

To workaround the problem, I've used the following code, i.e. basically sub-classed NSApplication and overridden sendEvent.

    
@interface NSObject (CSSafePerform)
- (void)safelyPerformSelectorOnMainThread:(SEL)selector
			       withObject:(id)arg;
@end

@interface NSApplication (CSSafePerform)
- (void)safelyPerformSelectorOnMainThread:(SEL)selector 
				   target:(id)self
			       withObject:(id)arg;
@end

@interface CSApplication : NSApplication {
  BOOL processingEvent;
  NSMutableArray *performQueue;
}
@end

@implementation NSObject (CSSafePerform)
- (void)safelyPerformSelectorOnMainThread:(SEL)selector
			       withObject:(id)arg
{
  NSAssert ([NSApp isKindOfClass:[CSApplication class]],
	    @"safelyPerformSelectorOnMainThread not supported");
  
  [NSApp safelyPerformSelectorOnMainThread:selector 
 				    target:self
				withObject:arg];
}
@end

@implementation CSApplication

typedef struct {
  SEL selector;
  id  target;
  id  arg;
} PerformInfo;

- (void)performOnMainThread:(NSValue *)info
{
  if (processingEvent) {
    if (!performQueue)
      performQueue = [[NSMutableArray alloc] init];
    [performQueue addObject:info];
  } else {
    PerformInfo pi;
    
    [info getValue:&pi];
    
    [pi.target performSelector:pi.selector withObject:pi.arg];
    
    [pi.target release];
    [pi.arg release];
  }
}

- (void)safelyPerformSelectorOnMainThread:(SEL)selector
				   target:(id)target
			       withObject:(id)arg
{
  PerformInfo pi = (PerformInfo) { selector, 
				   [target retain], 
				   [arg retain] };
  
  NSValue *v = [NSValue valueWithBytes:&pi 
			      objCType:@encode (PerformInfo)];
  
  [self performSelectorOnMainThread:@selector (performOnMainThread:)
			 withObject:v
		      waitUntilDone:NO];
}

- (void)sendEvent:(NSEvent *)anEvent
{
  BOOL oldProcessingEvent = processingEvent;
  
  processingEvent = YES;
  @try {
    [super sendEvent:anEvent];
  } @finally {
    if (!oldProcessingEvent) {
      processingEvent = NO;
      if (performQueue) {
	NSEnumerator *e = [performQueue objectEnumerator];
	NSValue *v;
	
	[performQueue release];
	performQueue = nil;
	
	while ((v = [e nextObject]))
	  [self performOnMainThread:v];
      }
    }
  }
}

@end


Chris Suter, Coriolis Systems

