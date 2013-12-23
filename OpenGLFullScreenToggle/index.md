---
layout: page
title: OpenGLFullScreenToggle
---

I'm trying to find out how I can exit OpenGL fullscreen mode in a way that I can get back into it -- quickly, that is, without destroying and having to build contexts, colourmaps, etc. I am currently using the SDL library for window and event handling, but even with a simple bit of example code (VBL: http://developer.apple.com/samplecode/VBL/VBL.html) I can't seem to find the magic touch.

Applications like Acrobat provide a fullscreen mode which still allows other windows to be taken to the front. A game like Jedi Academy also allows exiting fullscreen mode. How do they do it??

As a workaround, I detect the current screen resolution before creating a new window+context. If that resolution matches the required resolution (and SDL_FULLSCREEN is set), I open a frameless, fullscreen window. Exiting than amounts to doing an orderWindow:out (minimisation doesn't work reliably).

As a side-question: I was under the impression that doing a CGCaptureDisplay before opening a fullscreen context would protect other apps from resized windows, if opening a lower-than-current true fullscreen resolution. This doesn't work (anymore?): after a tour into 640x480 mode, I have to size back ALL my XCode and Terminal windows.... Workarounds???

Thanks!
Ren� Bertin
(rjvbertin!hotmail)

----

I have a program running which uses fullscreen mode also- that is I can go full screen as well. I have a custom view in this window (where I show some opengl stuff) and some buttons/controls.. 

The problem is this..
Once I go to the fullscreen and come back, my normal sized window cannot display the custom view but only displays the normal window with controls and without the custom view itself.. 

I read somewhere that in order for any other views to be shown, they should be made subviews of the content view. I have no clue how to do that. 

Also if anyone thinks there is another way around this, it could be helpful...
Below is the relevant part of the code...

    

-(IBAction)fullScreenid)sender
{
if (fullScreenMode)
{
[fullScreenWindow close];
[startingWindow setAcceptsMouseMovedEvents:YES];
// [startingWindow setContentView:];
[startingWindow makeKeyAndOrderFront:nil];
// [startingWindow makeFirstResponder:myGLView];
fullScreenMode = NO;
} else
{
unsigned int windowStyle;
NSRect contentRect;
startingWindow = [NSApp keyWindow];
windowStyle = NSBorderlessWindowMask;
contentRect = [[NSScreen mainScreen] frame];
fullScreenWindow = [[NSWindow alloc] initWithContentRect:contentRect
styleMask:windowStyle
backing:NSBackingStoreBuffered
defer:NO];
[startingWindow setAcceptsMouseMovedEvents:NO];

if(fullScreenWindow != nil)
{
[fullScreenWindow setTitle:@"GL Viewer"];
[fullScreenWindow setReleasedWhenClosed:YES];
[fullScreenWindow setAcceptsMouseMovedEvents:YES];
[fullScreenWindow setContentView:myGLView];
[fullScreenWindow makeKeyAndOrderFront:myGLView];
[fullScreenWindow setLevel:NSScreenSaverWindowLevel -1];
[fullScreenWindow makeFirstResponder:myGLView];
fullScreenMode = YES;
}
}
}



----

Please see HowToUseThisSite and related pages to learn how to use formatting on this wiki.

*Then check out NSWindowFullScreen and LearnOpenGL*

Linked on other pages here: http://developer.apple.com/samplecode/NSOpenGL_Fullscreen/NSOpenGL_Fullscreen.html

