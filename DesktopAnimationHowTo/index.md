---
layout: page
title: DesktopAnimationHowTo
---

I'm looking for some beginner level examples of how to do animations on the desktop. much like NekoOnDesktop and iLuxo.

while Neko has source, it is way too complex to digest at this point.

iLuxo - http://ios.free.fr
Neko - http://homepage.mac.com/takashi_hamada/Acti/MacOSX/

----

**Screen Mate clone discussion**

Basically, I d like to make something PC users have had for quite some time but that I couldn't find anywhere for mac: Screenmate... yeah, it's useless but I want it all the same...  I'm coming from RealBasic, and RealBasic can't do a screenmate... What I want is an app that would run in the background, not taking more than 5% of the processor time... The app would launch randomly some small animations of my character on the desktop... Is it possible to do such a thing with cocoa?

----

If you look through the sample projects installed by Developer Tools, I believe you will find one showing how to make a window in the shape of some writing. (If not, it's probably on the Apple site or something.) You could try adapting that to make a window exactly the shape of your animation, and have that romp across the screen. Drawing outside of a window is simply not allowed in Mac OS X, AFAIK.

I think it's possible for background apps to have windows, though I never looked into it myself. Alternatively, you could try making your screenmate a ScreenSaver.

----

I already thought of doing it with a window... for it to be in front of everything else shouldn't be a pbm using a modal dialogue... RealBasic could do so to but custom shape window were a real pain so an animated window... cocoa seems more efficient about this... maybe I ll use my screenmate as a screensaver too but the main objective is to have it as a screenmate first

----

I remember something about Apple posting an example of a program with non-standard shaped windows.  You might try looking at that.  As for interacting with folders, look into methods of getting a screenshot, and then interacting with things as they appear in your screenshot. --OwenAnderson

----

That would need pattern matching to recognise items...

----

You don't necessarily need to have a window that is the same shape as your animation (which may be problematic since stepping your animation implies changing the shape, and hence changing the window's shape, which may be heavy to do several times a second).  You could probably make do with a fully transparent window without a title bar.  The only way I know to make a window without a titlebar is do use the undocumented NSThemeWindow class, which may of course change its API or disappear entirely with a new OS release.

----

First of all, Carbon allows you to do some things with windows that Cocoa doesn't (for instance making transparent windows opaque for events).

Link against Carbon.framework and look in MacWindows.h. Specifically the Overlay window class:

From the header file:

"An overlay window is a completely transparent window positioned above all other windows. Overlay windows are intended as a replacement for the pre-Carbon practice of drawing directly into the window manager port; by creating a full-screen overlay window and drawing into it, you can draw over any window in any application without disturbing the contents of the windows underneath your drawing. After creating an overlay window, you should use CGContextClearRect to clear the overlay window's alpha channel to zero; this ensures the initial transparancy of the window. You must use CoreGraphics to draw into an overlay window to preserve the transparency of the window. Overlay windows are initially placed in the overlay window group, given a modality of kWindowModalityNone, and given an activation scope of kWindowActivationScopeNone. Available in Mac OS X."

  kOverlayWindowClass           = 14,
So, to use:

    

NSWindow cocoaWindow;
Rect windowSize;
WindowRef *newWindow;
OSStatus err;

windowSize.left=0;
windowSize.right=800; // Or whatever...
windowSize.top=0;
windowSize.bottom=600;

err=CreateNewWindow(kOverlayWindowClass,kWindowNoAttributes,&windowSize,newWindow);

if(err!=noErr)
{
// Whatever
}

cocoaWindow=[[NSWindow alloc] initWithWindowRef:newWindow];



You can do it pure Cocoa though, as shown in the Round Transparent Window example at http://developer.apple.com/samplecode/Sample_Code/Cocoa/RoundTransparentWindow.htm

As for interacting with folders, AppleScript is the way to go (and can be used through the NSAppleScript class). An example script is:

    
tell application "Finder"
	get the position of the first folder of the desktop
end tell


(And icons can be obtained (I think) through NSWorkspace...)

SamTaylor

