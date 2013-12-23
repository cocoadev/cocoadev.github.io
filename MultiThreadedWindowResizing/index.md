---
layout: page
title: MultiThreadedWindowResizing
---

Calling a method that contains the line     [mainWindow setFrame:frame display:YES animate:YES]; from a secondary thread causes the window to become a giant white rectangle until it finishes resizing.  What gives!?  Anyway I can get arround this annoying little bug?

----

Yep.  Call -setFrame:display:animate: in your main application thread.  The easiest way to do this is probably to add a category to NSWindow as such:
    
@implementation NSWindow (MultiThreadedResizing)
- (void)windowResizeShim:(NSValue*)inNewFrame
{
    [self setFrame:[inNewFrame rectValue] display:YES animate:YES];
}
- (void)setFrameInMainThread:(NSRect)inNewFrame
{
    [self performSelectorOnMainThread:@selector(windowResizeShim:)
        withObject:[NSValue valueWithRect:inNewFrame] waitUntilDone:YES];
}
@end


----
Awesome!  Does anybody know how Cocoa know which thread is the main thread, and why I can't call AppKit methods from secondary threads -- the documentation seemed to indicate that I should be safe!

----

How Cocoa knows which thread is the main thread? Hard to say - it could be done any number of ways. That's like asking how the system knows your process's pid. It just does. ;-)

Or are you asking how someone in general would find out which thread is the main thread? You could use pthread_main_np to find out if you're on the main thread. See /usr/include/pthread.h for the brief comment describing it. If you want the main run loop, you could call CFRunLoopGetMain(). (How does CF know? It probably stashes it into a global somewhere.)

I don't know the answer to the specific non-threadsafety problem you were having; I keep hearing that most of AppKit is supposed to be safe, at least since about 10.2 or so, but that in practice there may be small bugs. Don't necessarily blame AppKit though -- the bug might be in your code, too. :-) --DrewThaler

