---
layout: page
title: SetLevel
---



I am very new to Cocoa programming, and for a little bit of an experiment I wanted to create a kiosk'ed window i.e. a Window floating above a black background, kind of like the Authenticate window when you wake a mac from sleep.

I have successfuly managed to fade the display to black, capture all displays and fade back out and release displays, and prevent interupts from force quit etc.. however I am still unable to get the window level above that of the black Shielding window level, I have read that you must use this...

[window setLevel: CGShieldingWindowLevel?() + 1];

... setting window like this...

NSWindow *window = ... your window ...;

... however what goes in the '... your window ...' bit as this is obviously meant to be a reference to the window you are applying the action too, however as a newbie I have no idea what this reference is, or how it would be obtained.

--TomLynch

----

It depends where you're writing the code.
Inside the window's controller, you access the window with [self window].

----

I am very new to this and am just experimenting with my own thing for the moment as its quite different to what im used to.
 
Ok I tried that, I have created, instanated and created files for a class called controller, and I have 1 outlet and 1 action, the outlet reads the value of a tick box (hideBtn) and the action does some thing when the tick box changes state (update:) now when you hit update it goes off and executes some code within an if this if checks the status of the tick it ticked it does one thing other wise some thign else... Heres the code in that controller class...

    

#import "Controller.h"

@implementation Controller

- (IBAction)update:(id)sender
{

	if ([hideBtn state] == 1) {
		
		// Prepair for fade out
		CGDisplayFadeReservationToken reservationToken;
		CGAcquireDisplayFadeReservation(kCGMaxDisplayReservationInterval,
										&reservationToken);
		
		// Fade out
		CGDisplayFade(reservationToken,
					  0.5,
					  kCGDisplayBlendNormal,
					  kCGDisplayBlendSolidColor,
					  0, 0, 0,
					  true);
		CGReleaseDisplayFadeReservation(reservationToken);
		
		// Capture Display, Prevent system keys
		CGCaptureAllDisplays();
		SetSystemUIMode(kUIModeAllHidden,kUIOptionDisableAppleMenu | kUIOptionDisableForceQuit | kUIOptionDisableHide | kUIOptionDisableProcessSwitch | kUIOptionDisableSessionTerminate);
		
		//////////////////
		// DOESN'T WORK //
		//////////////////
		
		window = [self window];
		[window setLevel: CGShieldingWindowLevel() + 1];
		
		//////////////////
		// DOESN'T WORK //
		//////////////////
		
	} else if ([hideBtn state] == 0) {
		
		// Prepair for fade in
		CGDisplayFadeReservationToken reservationToken;
			CGAcquireDisplayFadeReservation(kCGMaxDisplayReservationInterval,
											&reservationToken);
			
			// Release Screen, Allow system keys
			SetSystemUIMode(kUIModeNormal, 0);
			CGReleaseAllDisplays();
			
			// Fade in
			CGDisplayFade(reservationToken,
						  0.5,
						  kCGDisplayBlendSolidColor,
						  kCGDisplayBlendNormal,
						  0, 0, 0,
						  true);
			CGReleaseDisplayFadeReservation(reservationToken);
			
			// Return window to normal level
			window = [self window];
			[window setLevel: NSNormalWindowLevel];
	}
	
}

@end



The log says...

    
[Session started at 2006-08-10 16:31:33 +0100.]
2006-08-10 16:31:35.045 KIOSK[799] *** -[Controller window]: selector not recognized [self = 0x3016e0]
2006-08-10 16:31:35.049 KIOSK[799] *** -[Controller window]: selector not recognized [self = 0x3016e0]
2006-08-10 16:31:35.813 KIOSK[799] *** -[Controller window]: selector not recognized [self = 0x3016e0]
2006-08-10 16:31:35.816 KIOSK[799] *** -[Controller window]: selector not recognized [self = 0x3016e0]


.. and Xcode has a warning ..

    
warning: 'Controller' may not respond to '-window'


----
NSWindowController responds to window - your controller is probably just a NSObject.
In which case, add an outlet to your controller (in IB click the controller, select the Classes tab, see attributes in the Inspector panel), name it myWindow and connect it to the window object from within Interface Builder.

write a window method that returns the outlet's content:
i.e.
    
- (NSWindow *) window
{
    return myWindow;
}


this should be it.
PS note that you either have to regenerate your controller's H file after adding the outlet from InterfaceBuilder or add the declaration yourself.

----

I now get the message

    

[Session started at 2006-08-11 14:38:48 +0100.]
2006-08-11 14:38:50.103 KIOSK[1233] PSsetwindowlevel, error setting window level (1001)

KIOSK has exited with status 0.



However that message dissapears if i set...

    

[myWindow setLevel: CGShieldingWindowLevel() +1];



..to..

    

[myWindow setLevel: NSScreenSaverLevel];



.. however although no errors occour this still doesnt do any thing to the window level, at least not in a visable way, I also tried...

    

[myWindow setLevel: NSScreenSaverLevel + 1];



.. and that didnt work, NSScreenSaverLevel is the higest possible level and +1 sets it above that so I am unsure as to why it isnt working unless for some reason its not pointing at the right window

--TomLynch

----

SOLVED > I think realised I haden't tried this...

    

[myWindow setLevel: CGShieldingWindowLevel()];



Thanks for your help it is very much appreciated. Out of interest who was the person(s) who helped me?

--TomLynch

----

Sorry - kind of new to this Wiki thing so I tend to forget signing my replies.
-- CristianDraghici

----
the windowlevel-contstants provided by AppKit are infact mappinfgs to enum-values in CoreGraphics (Quartz).
there you'll find keys such as kCGCursorWindowLevelKey
not sure what level that is exactly, but it sounds rather high..

see CGWindowLevel.h in QuartzDisplayServices for details

.arri

