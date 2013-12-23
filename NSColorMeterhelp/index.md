---
layout: page
title: NSColorMeterhelp
---

This is a slightly modified version of Michael Beam's Cocoa Color meter.  I would like the slider float values (RED, GREEN, BLUE, ALPHA) to be added/inserted into the "codeString" so when it is displayed in the textView it can be copyied and pasted into external code.

Here's the xcode download: http://www.bpstudio.com/SOFTWARE/NSColor%20Meter.sit.hqx

Here's the main code:

    
#import "Controller.h"

@implementation Controller

- (IBAction)setAlpha:(id)sender
{

    alphaValue = [sender floatValue];
   [alphaField setFloatValue:alphaValue];
   [alphaSlider setFloatValue:alphaValue];
   [self updateColor];
	
	
}

- (IBAction)setBlue:(id)sender
{

    blueValue = [sender floatValue];
   [blueField setFloatValue:blueValue];
   [blueSlider setFloatValue:blueValue];
   [self updateColor];
	
	
}

- (IBAction)setGreen:(id)sender
{

	greenValue = [sender floatValue];
	[greenField setFloatValue:greenValue];
        [greenSlider setFloatValue:greenValue];
	[self updateColor];
	
}

- (IBAction)setRed:(id)sender
{

	redValue = [sender floatValue];
	[redField setFloatValue:redValue];
        [redSlider setFloatValue:redValue];
	[self updateColor];

}

- (void)updateColor
{

    NSColor *aColor = [NSColor colorWithCalibratedRed:redValue green:greenValue blue:blueValue alpha:alphaValue];
	[colorWell setColor:aColor];
    [self updateCode];
}

- (void)updateCode

{

//I would like to insert the floatValues into the "codeString" and have them update when slider are moved.
//This way one could copy and paste the output into external code without re-typing the values.

    NSString *codeString = @"NSColor *myColor = [NSColor colorWithCalibratedRed:redValue green:greenValue blue:blueValue alpha:alphaValue];";
    [codeText setString:codeString];
	
}

@end


----

Try this:

    
- (void)updateColor
{
  NSColor *aColor = [NSColor colorWithCalibratedRed:redValue 
                     green:greenValue 
                     blue:blueValue 
                     alpha:alphaValue];
  [colorWell setColor:aColor];
  [self displayCodeForColor:aColor];
}

- (void)displayCodeForColor:(id)aColor 
{
  NSString *codeString = [NSString stringWithFormat:
    @"NSColor *myColor = [NSColor colorWithCalibratedRed:%ff green:%ff blue:%ff alpha:%ff];",
    [aColor redComponent],[aColor greenComponent],[aColor blueComponent],[aColor alphaComponent]];

  [codeText setString:codeString];
}


Note that your **updateCode** method has been replaced in the above by **'displayCodeForColor:aColor**' (your .h file needs to be updated to reflect this change). 

-PaulPomeroy

----

Thanks Paul!

This is what I ended up doing:

    

- (void)updateCode

{

float R = redValue;
float G = greenValue;
float B = blueValue;
float A = alphaValue;


    NSString *codeString = [[NSString alloc] initWithFormat:@"NSColor *myColor = [NSColor colorWithCalibratedRed:%.3f green:%.3f blue:%.3f alpha:%.3f];",R, G, B, A];
    [codeText setString:codeString];

}



Sean

