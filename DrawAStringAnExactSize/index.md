---
layout: page
title: DrawAStringAnExactSize
---



A relatively simple thing is sending me around the bend here...

I would like to draw a string (that may be of any number of characters) an exact size. So, for example, I have the string "abcdefg", and I want it to be drawn on screen 200 pixels wide (and a maximum of 100px all say), I need a way of figuring out the font size from that info.

So far I have had no luck at all.

I am using Q2D to draw into an NSView subclass. Here is what I have tried so far:

NSAttributedString: Has drawAtPoint: ... good. Gives me a size attribute, nice, I can figure out a resizing to fit the desired space from this I believe. However, I cannot draw it into a context of my choice (a CGLayer)- or have found no way to do so. Also, infuriatingly seems not to pick up changes to its attributes dictionary when being altered by the context (scale/move etc.).

Quartz2D text functions: can't tell me how big it's going to draw the text string on screen until after it's been drawn, and then, only can find out the x value of the size, no y value means I can't vertically center this text. This is easy to draw to a context of my choice however. Once possibility is to draw a big (100pt +) version of my text to the layer and then scale it down to the correct size, but fuzziness enues.

NSLayoutManager (etc.): the cocoa layout classes, almost did the trick, but the whitespace spacing from a set coordinate (0,0) grows and shrinks as the font size changes and I can find no way to nullify this value, i.e. I can find out the exact width and height of my text, but I cannot position it properly as I can't get a handle on the actual coordinate where the text starts, only this whitespace, which differs with font size, and so makes perfect positioning impossible when scaling the font size.

ATSUI: not looked at it, but seems carbon oriented, and stressful.

So, in short: Arg! Such a simple thing... how should I go about doing it?!

Thanks!

----

You could try something like this for getting a font that fits X pixels wide (warning untested!):
    
NSString *myString = @'abcdfg";
float fontSize = 10.0;
int maxWidth = 100;
NSAttributedString *astr = nil;
do
{
    NSFont *font = [[NSFontManager sharedFontManager] fontWithFamily:@"Lucida Grande" traits:0 weight:1.0 size:fontSize];
    [astr release];
    astr = [[NSAttributedString alloc] initWithString:myString attributes:[NSDictionary dictionaryWithObject:font forKey:NSFontAttributeName]];
    fontSize = fontSize + 1.0;
} while ([astr size].width < maxWidth);

[astr drawAtPoint:NSZeroPoint];


*Urgh. At least use a binary search. (To binary search an unbounded space: try a small size. Then multiply it by two until it's too big. Then binary search between the resulting size and half of that.)*

----

If you want to do this in real time, and avoid the while loop, you could simply see how many pixels wide a font at, say, 10pt would be, and then create a scaling matrix of desiredWidth / actualWidth.

----

Hi, thanks for the input, I've settled on drawing at a default point size and scaling. I'm using NSAttributedString, as it has the handy 'size' attribute that allows me to do this.

However - I'd like to incorporate Core Image into the drawing, but I believe that I have to draw into a CGLayer to take advantage of it... NSAttributedString doesn't let me specify a context to draw in - so I think I'm right in saying it just draws in the NSView's main CGContext, is there a way I can get it to draw in the context of my CGLayer - does lockFocus: achieve this?

Thanks for any help!
*Maybe something in CIImage or CoreImage would help?*

----

Ive created an ATSUIWrapper for C++ that is really clean and easy to learn.  It basically cuts down the hundred something lines you need to draw text with ATSUI into 5 lines.

It can be found here, along with an example and a short blog post: http://aquaticfire.com/blog/?p=12

~ MattTavares

----

You can get Cocoa drawing to draw into any CGContext by doing something like the following (typed directly into wiki, buyer beware):

    
NSGraphicsContext *oldCtx = [NSGraphicsContext currentContext];
[NSGraphicsContext setCurrentContext:[NSGraphicsContext graphicsContextWithGraphicsPort:myCGContextRef flipped:NO]];
... draw stuff here ...
[NSGraphicsContext setCurrentContext:oldCtx];


----

You realize that drawing text at 10 points and scaling 10x is not the same as drawing at 100 points. Small sizes are hinted, and won't look identical to the larger size. There may also be some subtle difference in metrics.

The best advice I saw was to do a little iteration, which avoids this problem.

