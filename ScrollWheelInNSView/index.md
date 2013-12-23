---
layout: page
title: ScrollWheelInNSView
---



I have a subclass of NSView, and I'd like to have it receive the scrollWheel: event, however it does nothing... it receives other events such as mouseDown:, mouseUp: just fine... but scrollWheel:, nada, zip. Even overriding it on NSWindow doesn't work!

My override method looks like:

    
- (void)scrollWheel:(NSEvent*)theEvent {
	NSLog(@"test");
}


What's wrong?

----

Is your view contained in another view which could be eating the event?

----

Its directly ontop of NSWindow.

----

Does it accept first responder? Please post more code - the one liner above doesn't really help much.

----

The initial first responder is my custom view.

What other code would be relevant? :-/

----

Do you have

    
-(BOOL)acceptsFirstResponder
{
    return YES;
}


in your view?

----

I do now and its not helping :(

----

You also need to override -becomeFirstResponder and have that return YES. 

----

Even with that it still isn't working *sigh* :-(

----

I just tried to subclass a NSView in Xcode and I set it in Interface Builder as custom class of a NSView I dropped onto the window.
My subclass receives scroll wheel events very well. And this view is not first responder and have no "becomeFirstResponder" nor "acceptsFirstResponder" methods in it.

I think you have problems with Xcode or IB. Can you try to "clean all targets" then rebuild your entire project in Xcode ?

Bru.

----

I think so too, its a bug it seems... the code above didn't work either. :-/


----

I got it! Since I was using NSBorderlessWindowMask, I had to add this to my NSWindow:

    
  - (BOOL)canBecomeKeyWindow
    {
        return YES;
    }


----
----

Hello, I have a new question about scrollWheel: actions and views, this time an NSOpenGLView:
I'm doing an OpenGL tutorial, and has managed to get my objects to scale when I use the scroll Wheel. All this works fine in windowed mode, but when I let the NSOpenGLView go fullscreen, I can't capture the scrollWheel: action anymore.
Is there a solution, or should I just resize my window to fit the screen, rather than going in true fullscreen mode? (expose etc disabled, without extra work) Thanks, -- EnglaBenny

