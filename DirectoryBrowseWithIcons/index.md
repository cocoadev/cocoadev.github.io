---
layout: page
title: DirectoryBrowseWithIcons
---



I've having some trobles with image scaling.

I'm making a ftp server with directory browser, and I want it to look like the finder list with the icons.

the problem is that when i scale the images it doesnt have the same quality as finder, is there a way to keep tha quality.

    
NSImage *image = [[NSImage alloc] initWithSize:NSMakeSize(18, 18)];
NSBitmapImageRep *rep = [[NSBitmapImageRep alloc] initWithData:
     [[[NSWorkspace sharedWorkspace] iconForFileType:@"ext"] TIFFRepresentation]];
[rep setSize:NSMakeSize(18, 18)];
[image addRepresentation:rep];


I've tried this one, but the program is running slow when i use it.

tnx
----

I haven't tried this, but using this code you make fewer calls, and I think the image will select the appropriate icon size and scale from that (in this case, it should scale from the 32x32 member)

    
NSImage *icon = [[NSWorkspace sharedWorkspace] iconForFileType:@"ext"];
[icon setSize:NSMakeSize(18, 18)];


EnglaBenny

----

By all means, use 16x16 as your size. The native sizes for icon are 16, 32, 48 and 128 px (+ 256 since Tiger). If you use 16 px, you'll get an image scaled by the artist, which looks much better than the one scaled by the system (and it's faster, too!).

----

Instead of using     setSize: use     drawInRect:fromRect:operation:fraction:.

    
- (void)drawIcon:(NSImage *)image inRect:(NSRect)destRect {
    [image drawInRect:destRect 
            fromRect:NSMakeRect(0.0f, 0.0f, [image size].width, [image size].height)
            options:NSCompositeSourceOver
            fraction:1.0f];
}



----

You can use IconFamily which makes it much easier. It's also part of OmniFoundation.
http://homepage.mac.com/troy_stephens/software/objects/IconFamily/

----

I ended using a combination of the above methods: (Using IconFamily)
Unfortunately IconFamily does not _always_ give the small icon (if there is no small icon in the icns file I assume) so you have to explicity resize it. Otherwise this would be even simpler.

    

- (NSImage *)getIconForPath:(NSString *)path {
	//set the desired size of icon
	NSSize canvasSize = NSMakeSize(15.0, 15.0);

	IconFamily *iconFamily = [IconFamily iconFamilyWithIconOfFile:path];
	NSImage *theIcon = [iconFamily imageWithAllReps];
	NSRect srcRect = NSMakeRect(0.0f, 0.0f, [theIcon size].width, [theIcon size].height);
	NSRect destRect = NSMakeRect(0.0f, 0.0f, canvasSize.width, canvasSize.height);
	NSImage *canvas = [[[NSImage alloc] initWithSize:canvasSize] autorelease];
	[canvas lockFocus];
	[theIcon drawInRect:destRect fromRect:srcRect
			 operation:NSCompositeSourceOver fraction:1.0f];
	[canvas unlockFocus];
	return canvas;
}


