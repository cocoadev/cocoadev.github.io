---
layout: page
title: ColorsMathAndMystery
---

Long story short: I've been working on a program that does a lot with color comparisons. I've been using a computational intelligence method (particle swarm optimization) to find factors that can be applied against a color's red, blue and green components to give an accurate measure of some color aspect (e.g., how bright it is compared to another color).

The following is one of the results (with the long floating point numbers being the "factors"). What I'm doing is, for each color component I'm comparing it to the other 2 components and applying a factor. So, for red I take red minus green and multiply it by a factor and then red minus blue, multiplied by another factor -- i.e., ((r-g)*rg + (r-b)*rb) where rg and rb are factors -- and I do the same for the green and blue components and then add everything up.

Here's my problem: it doesn't matter which color I feed to this algorithm, the result is always zero, nada, zip, ... a big fat nothing. 

I've looked at the formula I'm using and I can't for the life of me see how it can come up with the exact same answer for more than 16 million colors. There's gotta be something wrong with how the algorithm is constructed, but I don't see what ...

If you want to see for yourself, create a window with a text box and a colorwell. Link the colorwell to an action named **doIt** and make the text box an outlet named **textBox**. The following code should then work:

    
/* MyTest.h */

#import <Cocoa/Cocoa.h>

@interface MyTest : NSObject
{
    IBOutlet NSTextField *textBox;
}
- (IBAction)doIt:(id)sender;
@end


    
/* MyTest.m */
#import "MyTest.h"

@implementation MyTest

NSColor *c;

- (IBAction)doIt:(id)sender; {
	
	c = sender color] colorUsingColorSpaceName:[[NSCalibratedRGBColorSpace];
	
	float r = [c redComponent];
	float g = [c greenComponent];
	float b = [c blueComponent];
	
	float rg, rb, gr, gb, br, bg, cl;
	
	rg = -0.67133151010695070f;
	rb =  0.51569575657798890f;
	gr = -0.09174120742836643f;
	gb = -0.29095023307603560f; 
	br = -0.06389454610059392f; 
	bg =  0.28864006960254680f;

NSLog(@"(r-g)*rg=%f, (r-b)*rb=%f, (g-r)*gr=%f (g-b)*gb=%f, (b-r)*br=%f, (b-g)*bg=%f",
        (r-g)*rg,    (r-b)*rb,    (g-r)*gr,   (g-b)*gb,    (b-r)*br,    (b-g)*bg);	

  cl = ((r-g)*rg + (r-b)*rb) + ((g-r)*gr + (g-b)*gb) + ((b-r)*br + (b-g)*bg);
	
	[textBox setFloatValue:cl];
}

@end


When you've got it running, click on the colorwell and use the color panel to change the color. You'll see that the only thing that gets put in the textbox is 0.00 or -0.00.

Here's an example of the log output (with the line wrapped). If you add all of the numbers up, you get 0.

    
2004-10-05 23:39:00.611 MyTest[1452] (r-g)*rg=0.018392, (r-b)*rb=-0.016721, 
                                     (g-r)*gr=-0.002513 (g-b)*gb=0.001463, 
                                     (b-r)*br=-0.002072, (b-g)*bg=0.001451


Anybody have any ideas? Have I done something dumb and just can't see it?

--PaulPomeroy

----

Yeah, that is always 0, more or less.  You can rewrite the function for c1 as

    
cl = (rb + rg - br - gr)*r + (br + bg - rb - gb)*b + (gr + gb - rg - bg)*g


The parameters seem to have been chosen such that each of the parenthesized expressions is zero, so the values of     r,     b and     g don't matter.

�KenFerry

----

Thanks, Ken. I was just coming back online to say I'd figured it out, but you beat me to it. Interesting how you can lock in to one way of looking at something and completely miss something like this. Funny, too, how the fact that the computational intelligence program had chewed on this for quite awhile before coming up with those numbers had me convinced it knew what it was doing... 

This reminds me of a story I heard many years ago about a military project in which they were using neural nets to analyze pictures. As I recall, they were trying to see if they could train the NN to detect hidden enemy tanks in the picture (they'd taken a bunch of pictures of some area, then brought in the tanks, hid them and took another whole bunch of pictures). Anyway, after a few thousand training runs, the NN started getting *really* good at detecting whether or not there was a hidden enemy tank in the picture. Everybody was really pleased. Then they took some more pictures and, lo and behold, the NN couldn't see an enemy tank in any of them, even when they weren't hidden. 

It was a big mystery until someone happened to notice that in the first set of pictures, the "no tanks" and "hidden tanks" photos were taken on two different days and on the day when the hidden tanks were there it was very cloudy. It ended up that the NN hadn't learned anything about hidden tanks, but it could tell you whether or not a picture was taken on a cloudy day.

There's probably some moral to that story ... 

--PaulPomeroy

