---
layout: page
title: ProblemDeallocationNSOutlineView
---

I've got some problem with programmatically creating a window showing a NSOutlineView object.

For some reason I do not want to use Interface Builder to create that window and view.
Somehow I manage to bring the window including the outline view on screen. For that I need
to use alloc-method several times. Going on in the program I want to hide only the NSOutlineView object (with NSScrollView and NSClipView) and freeing the memory. The window should stay alive.
I tried to use release at appropriate times. But, PROBLEM: when I release one of the views after its retainCount is down to 1, so it should be deallocated now, my application is crashing.
Error message: myApplication has exited due to signal 11 (SIGSEGV)

Can anyone please explain to me, how I can create the needed NSOutlineView and deallocate it the right way?

Strange observation: the crashing occours only when I use setDocumentView- or setContentView-method.

here is my test code that does not work:

----
MyOutlineView.h:
    
#import <Cocoa/Cocoa.h>
@interface MyOutlineView : NSObject
{
  NSWindow	 	*window;
  NSScrollView 		*scrollView;
}
- (IBAction)clear:(id)sender;
@end

----
MyOutlineView.m:
    
#import "MyOutlineView.h"
@implementation MyOutlineView
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
  /* preparing some frame for window's contentView and all other views (unimportant now) */
  NSRect contentRect;
  contentRect.size.width = 270.;
  contentRect.size.height = 180.;
  contentRect.origin.x = 0.;
  contentRect.origin.y = 0.;
  /* any style for window (unimportant now) */
  unsigned int styleMask = NSTitledWindowMask | NSClosableWindowMask;
  /* allocating and initializing NSOutlineView, NSClipView and NSScrollView */
  NSOutlineView *outlineView = [[NSOutlineView alloc] initWithFrame:contentRect];
  NSClipView *clipView = [[NSClipView alloc] initWithFrame:contentRect];
  scrollView = [[NSScrollView alloc] initWithFrame:contentRect];
  /* setting outlineView as documentView of clipView and clipView as contentView of scrollView */
  [clipView setDocumentView:outlineView];
  [scrollView setContentView:clipView];
  /* allocating and initializing NSWindow */
  window = [[NSWindow alloc] initWithContentRect:contentRect styleMask:styleMask backing:NSBackingStoreBuffered defer:NO];
  /* setting scrollView as window's contentView */
  window contentView] addSubview:scrollView];
  /* showing window */
  [window makeKeyAndOrderFront:self];
}  

- ([[IBAction)clear:(id)sender
{
  /* cut connection between scrollView and window */
  [scrollView removeFromSuperview];
  NSLog(@"[scrollView retainCount] %d", [scrollView retainCount]);
  [scrollView release]; //scrollView got second retain from NSView's addSubview-method
  /* if you close the window before pressing command-o scrollView will be released by that ones. So at this point only one release would be enought. */
  NSLog(@"[scrollView retainCount] %d", [scrollView retainCount]);
  [scrollView release];//scrollView got first retain from alloc
  scrollView = nil;
  NSLog(@"[scrollView retainCount] %d", [scrollView retainCount]);
}
@end

