---
layout: page
title: LoadFromFileNSImage
---

I'm trying to create a simple image view application. I'm having a problem using NSImage initWithContentsOfFile.  They keep going to nil when I try to load the file.  I know that I can read the file, because I can load a NSData object using initWithContentsOfFile.  However, if I try to NSImage initWithData, the NSImage object still comes out nil.

I tried using the + [NSImage imageFileTypes] which should return an array of file types that NSImage should be able to read.  I keep getting an empty NSArray.

This is what the code looks like in main.m

    
    id temp = @"/Users/john/programming/CustomViewTest/build/CustomViewTest.app/Contents/Resources/full grid.psd";
    if( [fm fileExistsAtPath:temp] ) NSLog(@"file exists at %@" , temp ); else NSLog(@"file does not exists at %@" , temp );
    NSImage * picture = [[NSImage alloc] initWithContentsOfFile:temp];
    if( picture ) NSLog(@"Picture is not null"); else NSLog(@"Picture is null.");


The outout is 
   File Exists at . . . 
   Picture is null

If I get the path from an NSBundle, then everything works, but if the file is not in a bundle it doesn't work.

----

Try removing the space in the filename.

You could also just shorten this to     [NSImage imageNamed:@"full grid"]

----

Does NSImage even support Photoshop documents?  Try saving it as a TIFF, GIF, JPEG or PNG and see if it works then.  -- Bo

----

First, yes, NSImage supports PS files.  It loads just fine when I get the NSBundle path and initWithContentsOfFile.

The space in the name should be fine.  When I get the path from the NSBundle, it loads.  If I hard code the path, it doesn't.  Do I have to encode the filename to support URL stuff?

----

NSImage docs say: *initWithContentsOfFile: will look for an NSImageRep subclass that handles that data type from among those registered with NSImage.*

NSImageRep docs say: *By default, the files handled include those with the extensions �tiff�, �gif�, �jpg�, �pict�, �pdf�, and �eps�.*

I imagine when going through NSBundle you're getting some QuickTime translation on the .psd file.

----

You can see what formats NSImage will load by running this:
    
    NSLog(@"%@", [NSImage imageFileTypes]);


PhotoShop files are deffinetly on the list...

----

**Beware** when using     frogImg = [NSImage imageNamed:@"GreenFrog"]; inside a bundle/plugin. It will not work, unless the image "GreenFrog" exists in the main bundle's resources folder (but usually you're referring to an image inside the bundle's resources folder). You would want to use something like this instead: 
    
frogImg = [[NSImage alloc] initWithContentsOfFile:[[NSBundle bundleForClass:[self class]] pathForResource:@"GreenFrog" ofType:@"tif"];

