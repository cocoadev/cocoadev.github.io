---
layout: page
title: CoregraphicsFullscreen
---



I need help on howto make an cocoa application go into fullscreen with coregraphics like the iphoto slideshow or dvd player fullscreen
Because i tried CocoaDev Central's tutorial but it did not work for me

I can help you, but one question.... Panther or Tiger?
-- JasonTerhorst


Tiger 

How do I implement it into an application. And how can i make a app go into fullscreen?

----

I haven't done anything at all with Core Graphics, but if you are displaying what you need in an NSView or any other object within a window, then you can turn off the menu bar and dock and make your window the size of the screen and set it at screen saver level.

This is a snippet of code from Apple's example for custom windows. Place this in your window subclass.

    
//In Interface Builder we set CustomWindow to be the class for our window, so our own initializer is called here.
- (id)initWithContentRect:(NSRect)contentRect styleMask:(unsigned int)aStyle backing:(NSBackingStoreType)bufferingType defer:(BOOL)flag {

    //Call NSWindow's version of this function, but pass in the all-important value of NSBorderlessWindowMask
    //for the styleMask so that the window doesn't have a title bar
    NSWindow* result = [super initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:NO];
    //Set the background color to clear so that (along with the setOpaque call below) we can see through the parts
    //of the window that we're not drawing into
    [result setBackgroundColor: [NSColor clearColor]];
    //This next line pulls the window up to the front on top of other system windows.  This is how the Clock app behaves;
    //generally you wouldn't do this for windows unless you really wanted them to float above everything.
    [result setLevel: NSScreenSaverWindowLevel];
    //Let's start with no transparency for all drawing into the window
    [result setAlphaValue:1.0];
    //but let's turn off opaqueness so that we can see through the parts of the window that we're not drawing into
    [result setOpaque:NO];
    //and while we're at it, make sure the window has no shadow
    [result setHasShadow:NO];
    
    //This is my portion ********    
    NSRect  screenFrame = [[NSScreen mainScreen] frame];
    [self setFrame:screenFrame display:YES];
    //End of my portion ********
    
    return result;
}

// Custom windows that use the NSBorderlessWindowMask can't become key by default.  Therefore, controls in such windows
// won't ever be enabled by default.  Thus, we override this method to change that.
- (BOOL) canBecomeKeyWindow
{
    return YES;
}


That should give you a large window of which has no background, title bar or border. Covers your whole screen and cannot be moved. All objects that are placed within this window by IB will be visible. Turning off the menu bar is not entirely necessary at this point, because the window when drawn will be over the menu bar. But it might be a good idea to anyway to clean things up a bit and avoid conflictions.

    
[NSMenu setMenuBarVisible:NO];


If you would like to set the window to be a black sheet, rather than a totally transparent sheet then change
    
[result setBackgroundColor: [NSColor clearColor]];
[result setOpaque:NO];

to
    
[result setBackgroundColor: [NSColor blackColor]];
[result setOpaque:YES];


Of course, the object that you will be using for your core graphics, should be covering the whole window - so borders and background colours will hopefully handled by that object instead. Unfortunately I don't know how the core graphics works.

Hope this helps!
Cheers, Louis
----

As an alternative to setting the window level very high, I *highly* recommend keeping the window at a normal level, and calling this:
    
SetSystemUIMode(kUIModeAllHidden, 0); // to enter fullscreen
SetSystemUIMode(kUIModeNormal, 0); // to exit fullscreen

This will get rid of the Dock and the menu bar so that nothing is covering your window, but it has advantages. Namely, the user can still cmd-tab out of your application, and your app doesn't become terminally un-killable if it freezes. Bumping the window up to the screensaver window is high enough to cover the Force Quit window, as well as the debugger window, so when you write a bug it can be quite the royal pain.
----
Where exactly is this apple custom view demo? And how do you set up everything else? are there any tutorials for this... Because im new to Obj-c
----
Good point about the screen saver level window. But that call doesn't hide status window level apps, it virtually does the same as the cocoa call:
    
[NSMenu setMenuBarVisible:NO];

Which kills the menu bar, and dock. So you could use either call, but you will have to set your window to something higher than the status level window, but not as high as the screen saver level window. Which is only one thing; pop up menu window level, which I don't know if writing a bug becomes another royal pain. But the force quit window does cover it! There should be another way rather than having hierarchal battles with window levels. None the less to use the alternate call you have to add the carbon framework into your header.
The alternate call is a carbon call, so in the header you will have to add
    
#import <Carbon/Carbon.h>

where you see
    
#import <Cocoa/Cocoa.h>

To be able to use it without Xcode going Beep Beep rudely in your face :P

So the header file of say appController would be like this:
    
/* appController */

#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

@interface appController : NSObject
{
}
@end


Then you can stick it anywhere in your method file. I would suggest in the action where you switch from windowed view to full screen. Or if its full screen from the start, then in
    
- (void)awakeFromNib


BTW, the Apple Custom Window demo is here : http://developer.apple.com/samplecode/RoundTransparentWindow/RoundTransparentWindow.zip

Isn't there anything in Core Graphics that could help you? There are plenty of full screen sample code thingo's at apple, here are some of them;
http://developer.apple.com/samplecode/NSOpenGL_Fullscreen/NSOpenGL_Fullscreen.html
http://developer.apple.com/samplecode/FullScreen/FullScreen.html

Good Luck!
Louis
----

Apple added some fancy stuff for window fade ins/fade outs/resizing/animation in Tiger, that was my reason for asking the OS version above. Try NSAnimation for that, but the other major thing I was going to suggest has already been answered. I'm a bit slow. -- JasonTerhorst

----

Beware, as it seems Apple added the 

- (NSRect)constrainFrameRect:(NSRect)frameRect toScreen:(NSScreen *)aScreen

method to the NSWindow class. I think it wasn't there before (can anybody verify this?). It automatically constraints the size and position of your NSWindow to the screen, so you can't move your window outside of it. You have to subclass NSWindow to overload the method.
-- ThomasSempf

I've seen something like that for a while now. I remember trying to use it before, and then I ended up not using it, but can't remember why. But I don't know of anyone that uses it, and it's not set to do anything unless you send it something, so it shouldn't pose a problem for this exercise. Key word: "shouldn't", because it's possible that something funky and unpredictable could still happen. -- JasonTerhorst

Well, under the standard implementation of NSWindow in Tiger the method is called automatically! So all windows are constrained to screen parameters, at least the top border is always inside the screen. -- ThomasSempf

----
It is also possible to capture one or all of the displays - meaning only your app gets to draw to the screen (actually the other apps draw too but their drawing is hidden by a window that looks like the desktop). See the CGDisplay documentation for more info.

