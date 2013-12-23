---
layout: page
title: LockFocusProblem
---

I am Xavier and I want to edit an existing NSImage.
Actually I need to mask a particular rect in the Image.
For that I locked the Image and did the Following Operation.
Please see the code Below\n

    
-( BOOL ) maskRegion: ( NSRect ) rectForMasking: ( RGBColor ) maskingColor
{
	@try
	{
		// First make a NSColor from the RGBColor
		float fRed		= (float)( maskingColor.red / 255.0 );
		float fGreen	= (float)( maskingColor.green / 255.0 );
		float fBlue		= (float)( maskingColor.blue / 255.0 );
		
		NSColor* fillColor = [ NSColor colorWithCalibratedRed:fRed
										green: fGreen 
										blue:  fBlue
										alpha:1 ];
		
		// Now set current drawing focus to our image and fill the rect with
		// the masking rect.
		[ a_imgImage lockFocus ];
		[ fillColor set ];
		NSRectFill( rectForMasking);
		[ a_imgImage unlockFocus ]; 
		
		return TRUE;
	}
	
	@catch( NSException *exception )
	{
		return FALSE;
	}


     I am having a huge amount of memory leak from this code when I process a lot of Images.
     On debugging I found that the NSImage's lockFocus is causing the Problem.
     When I comment the lock and Unlock Focus statements the problem is not happening.
     But those statements are a must for the Process.

    Do I have to do any extra steps before or after the lockFocus and UnlockFocus methods.
   Please help.
   Thanks in advance
   Xavier


Are you leaking, or is the amount of memory just going up. Locking focus on an image has to create an editable image rep for the image - an NSCachedImage rep. So, you'll probably see your memory usage double if you load a bunch of images, and then call this function on each of them. If you need to keep the images around, maybe you could remove the original NSImageRep, or, turn the cached image into a NSBitmapImageRep, and get rid of the original and the cache. If you're are keeping the images in memory, use an autorelease pool in your loop so they get dealloced. Jon H.

