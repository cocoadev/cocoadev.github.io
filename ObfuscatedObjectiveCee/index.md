---
layout: page
title: ObfuscatedObjectiveCee
---



I was asked by a friend to create a program to create image files from icons. As I didn't feel like doing anything useful, I decided to do it. As I thought the project was a bit boring and sleep-inducing, I also decided to do it in a single line of code of nested calls:

    return [[NSBitmapImageRep representationOfImageRepsInArray:[[[NSWorkspace sharedWorkspace] iconForFile:filename] representations] usingType:( [[[NSUserDefaults standardUserDefaults] stringForKey:@"Format"] isEqualToString:@"tiff"] ? NSTIFFFileType : ( [[[NSUserDefaults standardUserDefaults] stringForKey:@"Format"] isEqualToString:@"gif"] ? NSGIFFileType : ( [[[NSUserDefaults standardUserDefaults] stringForKey:@"Format"] isEqualToString:@"jpg"] ? NSJPEGFileType : NSPNGFileType ) ) ) properties:NULL] writeToFile:[NSString stringWithFormat:@"%@/%@.%@", ( [[NSUserDefaults standardUserDefaults] stringForKey:@"SavePath"] ? [[[NSUserDefaults standardUserDefaults] stringForKey:@"SavePath"] stringByExpandingTildeInPath] : [NSString stringWithFormat:@"%@/Desktop/", NSHomeDirectory()] ), [[NSFileManager defaultManager] displayNameAtPath:filename], ( [[NSUserDefaults standardUserDefaults] stringForKey:@"Format"] ? [[NSUserDefaults standardUserDefaults] stringForKey:@"Format"] : @"png" )] atomically:NO];

It features different saving locations (set using defaults) and different file formats (also set using defaults).

Now, I understand completely that this is no good thing to do in general, but it does prove the point that ObjectiveCee has a very flexible syntax, and that the cocoa frameworks are very well made.

If anyone wants the project or app:
http://ittpoi.com/pingifyer.tgz
http://ittpoi.com/pingifyerproj.tgz

Anyway, now to my question: What is the most obfuscated objective C code you can think of?

/ DavidRemahl

----

From one of my own projects:

    
return [self class] alloc] initWithBytes:[[[NSData dataWithBytes:[rep bitmapData] length:([rep bitsPerPixel] * [rep samplesPerPixel] * [rep pixelsHigh] * [rep pixelsWide])] pixelsHigh:[rep pixelsHigh] pixelsWide:[rep pixelsWide] bitsPerChannel:[rep bitsPerSample] channelsPerPixel:[rep samplesPerPixel]]; // this unreadable mess makes an FFBitmap from rep


and

    
return [[[NSBitmapImageRep alloc] initWithBitmapDataPlanes:[_bytes mutableBytes] pixelsWide:_w pixelsHigh:_h bitsPerSample:_bpc samplesPerPixel:_cpp hasAlpha:[self hasAlpha] isPlanar:NO colorSpaceName:NSDeviceRGBColorSpace bytesPerRow:0 bitsPerPixel:0] autorelease];


(by the way... it'd be nice if the code blocks didn't force the horizontal scrollbar... Steven? :) )

Not as bad as yours, but still, not very readable. And soon to be replaced; the custom bitmap class I'm using is going to be a struct soon (for performance).

-- RobRix

----

In fact, I think that serious nesting as in your bitmap example can often dramatically increase the flow and readability of code. I would much rather read the code you gave than something that saves the intermediate values (length, etc) in temp variables and calls the method with those variables.

-- DavidRemahl

----

I think there's a balance to be met. I find ObjC code that wraps more than one line in PB is generally too difficult to read. Either the code should be factored into smaller groups (the "image files from icons" example is clearly gratuitous) or the formatting should better reflect the nature of the call:

    
return [[[NSBitmapImageRep alloc] 
                         initWithBitmapDataPlanes:[_bytes mutableBytes] 
                                       pixelsWide:_w 
                                       pixelsHigh:_h
                                    bitsPerSample:_bpc 
                                  samplesPerPixel:_cpp 
                                         hasAlpha:[self hasAlpha] 
                                         isPlanar:NO
                                   colorSpaceName:NSDeviceRGBColorSpace
                                      bytesPerRow:0 
                                     bitsPerPixel:0] autorelease];


See? RobRix example is now very readable. In fact, the code is the only readable part of this post -- all the paragraphs have been blown out into one or two lines by the ungodly long program examples above.

Further, I believe C code should strive to fit in 80 columns w/o sacrificing reasonable function/variable names. This is because I often need to debug w/ gdb in a terminal session, where readability in 80 columns is a definite plus. I hate having to make my terminals larger to fit code on the screen.

In any case, the compiler commonly optimizes out temporary variables. So there is no run-time performance cost for making your code more reasonable by using temporary variables.

My opinions,
-- MikeTrent

Agree completely with Mike's words. -- DavidRemahl

Myself also. I like using the aligning colon trick in long messages, it's nicely understandable. I used to do something similar with constructors in C++ back in the day (i.e. before I learned Objective-C and did something [relatively] *useful*�C++ and I do not get along well). -- RobRix

FWIW, The old PB used to auto-format by colons automatically if it noticed a multi-line ObjC statement. So, there was no reason not to use that kind of formatting. I wonder if the new PB will ever get that functionality back ...

-- MikeTrent

Oh wow, that's great... I like that idea. Maybe if we tell them how much that would mean to us... the Apple engineers seem pretty accommodating if you're nice about it. -- RobRix

Uhm, if you read the release notes, you'll see that that feature is part of the auto indentation capabilities in the last release! It needs to be activated in prefs, but apart from that you're ok. -- DavidRemahl

Not only do the Apple engineers rock, you do too, David. Thank you. [Update] Okay, it's activated, now how do I actually *use* it? Just typing in some silly tests ([bah bah:bah ... ]; for instance :) ) didn't work. Am I missing something obvious? -- RobRix

You need to do the returns yourself, and the colons will line up automagically. -- DavidRemahl

Aha. Many thanks. -- RobRix

