---
layout: page
title: AlwaysFirstresponder
---



I am working on a kiosk application and I am trying to force the application to the front and to make sure my kiosk stays front and active. I can set the window level above every thing else but I need to make sure the application is always active unless I choose otherwise I have been experimenting with the code below but nothing seems to work its like it ignores my code.

This is my code for my appcontroller.m 

#import "AppController.h"

@implementation AppController

- (void)applicationDidResignActive:(NSNotification *)note
{
	[NSApp activateIgnoringOtherApps:YES];
}

@end

It does not work and I really cannot understand why can any one help? It works if you use this code in Chapter 11 page 202 (challenge 1) of Cocoa programming for mac os x but it doesnt work in a plain standalone application. I really do not understand why.

Thanks

----

This topic is very well-covered in TN2062: http://developer.apple.com/technotes/tn2002/tn2062.html

----

I have read that article many times before however I need to make a non full screen window attain first responder and stay there. I could explain why but it would be a long and pointless explination as I know what I need to do just not how.

----

What do you mean by "first responder"?  Applications aren't "first responder" anything.  Do you mean you want your application to remain active all the time?

----

*"I could explain why but it would be a long and pointless explination as I know what I need to do just not how."*

May I suggest taking a slightly less arrogant approach to asking a technical community for help? As the reply above indicates, your terminology is suspect, therefore so is your concrete certainty that the solution you've selected for your goal even makes sense.

----

I want to start my reply by stating that I am always thankful of any insight or help provided, I am not being arrogant. I am only trying to highlight that it would be a quite long explanation however I will explain it.

I am creating a kiosk application which uses creates a window on every display at and makes it black with an alpha value of .75 and sets it to fill the screen. The reason I am creating these my self is because the normal CGCaptureAllDisplays() carbon function does not allow me to set the black masking windows to 0.75 alpha. By not using the the normal CGCaptureAllDisplays() I loose the functionality which prevents switching and force quit etc... I have re-implemented this by using SystemUIMode functionality however it is possible for my application to launch and go into kiosk mode without being key, obviously the windows are above the level of others however a window can be infront of another without being active (what I meant by first responder) this means any input made would clearly not make it into the inactive window. So what I would like to do it use applicationDidResignActive: to force my application back to being active if it were for some reason to loose this. However I have tried the above code and it didn't work. I put it into my AppController file and it didn't work however the idea for this solution came from Chapter 11 of Aaron Hilgass's - 2nd Edition - Cocoa Programming For Mac OS X (page 202) where he challenges the reader to make the application in question beep when it resigns active. The above code works fine in the challenge but when I copy and paste it into my application it fails. Its like the code is ignored because I have tried NSBeep() and NSLog() with no response either. I thought this might be because I need to initialize it or I have my AppController linked up incorrectly however thus-far I have been unsuccessful so I was hoping some one here with more knowledge and experience could help.

----

The method in question is a delegate method of NSApp. Control-drag a connection in IB from the File's Owner to your app controller and set the delegate method. This designates your app controller as the app delegate, so NSApp will let it know that "applicationDid" - in fact - "ResignActive:" ... Also, "first responder" has nothing to do with whether your app is active. Look the term up in the documentation (there's a whole focus on it) and make sure you use it properly or you will cause exactly the confusion above. 

----

Thanks! I am still learning these things I will do a bit more reading.

----

I happen to think your approach is non-optimal. If you can specify 10.3 or later, you can use     CGCaptureAllDisplaysWithOptions (CGCaptureOptions options); which currently only has one option - to disable the black fill. Thus you can capture your displays using this then simply place a 0.75 alpha mask window behind your others to create the effect you want, and all the other malarkey won't be needed. Also, one minor correction - CGCapture... etc functions are not Carbon, they are part of Core Graphics, hence the CG prefix. They have a C API and are part of the Application Services framework. You are not linking to Carbon when you use these functions. --GrahamCox

----

I did think of that however when I initially investigated it I think there was some thing which didn't work the way I wanted however I will have another look into it. Thanks!

----

I tried it out and a weird thing happens. Any thing set at the CGShieldingWindowLevel() is presented but my full screen black window isnt except in a small 50px border around any window above the shielding window. I think its a bug in the code because I remember this happening before I am basically creating my alpha masking windows over the shielding window, this does nothing its not visible for some reason its like the frame buffer isnt being updated any more. However when I come to add a window on my screen it updates the buffer and a small area around it about 50px to ensure the shadow is updated. I presume I need to workout how to get the frame buffer to update but I will have to look into this.

