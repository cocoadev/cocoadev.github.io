---
layout: page
title: ProgressIndicator
---

Is it possible to get the spinning progress indicator images like this?


[[[NSTableView _defaultTableHeaderSortImage] TIFFRepresentation] writeToFile: @"/sort-up.tiff" atomically: YES];


*I don't know, is it? You just gave exact code, and you're just a compile and a run away from finding out if it works!*

Yes but that is a NSTableView sort image, and not the spinning progress.....:)

*Ah, I see. The question was unclear. Sorry about the misunderstanding.*

**The images for widgets like this are buried somewhere in the system folder. A little googling will turn up dozens of sites that tell you where these system resources are ...**

----


* You realize that your table view operation was unsupported, right?  Apple can change that out from under you at any time.
* Try     -[NSProgressIndicator _resizedImage:] (equally unsupported)


*Yes i know its unsupported*

----

    
// assuming 'progress' is a spinning indicator (or any view, really)...
- (NSImage *)image 
{
    NSImage *progressImage = [[NSImage alloc] initWithSize: [progress frame].size];

        [progressImage lockFocus];
          [progress drawRect:[progress bounds]];
        [progressImage unlockFocus];

   return [progressImage autorelease];
}


----
The made the last doe work by doing some modifications.  *wha??* -*I've looked trough it again and your code is working, I dont know what i did.....*

thanks

btw:
[NSProgressIndicator _resizedImage:] didnt work.....

*How so?  It worked when I tried it.*

I get an error when i compile....

----
And the error is ummm.... what?

**Please, don't say "missing argument" - we'd have to disown you ;)**

----

    
 -[[[NSProgressIndicator _resizedImage:] TIFFRepresentation] 
                    writeToFile: @"/sort-up.tiff" atomically: YES];

[[[NSProgressIndicator _resizedImage:] TIFFRepresentation]
                    writeToFile: @"/sort-up.tiff" atomically: YES];


I've tried both of them and the error is: * parse error before ']' token*

----

The line     -[NSProgressIndicator _resizedImage:] is standard objective-C notation.  It refers to a method that you could call like this: 

    
NSImage *image = [progressIndicator _resizedImage:0];


where progressIndicator is an instance of NSProgressIndicator.

