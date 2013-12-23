---
layout: page
title: IconBadgeDrawingGlitch
---

On this page I've posted a small image that best explains the problem that I'm seeing:

	http://www.hogbaysoftware.com/badgediconbug.gif

The left icon has a small white glitch in the bottom left corner. The right icon is drawn as I want and expect. I'm trying to figure out what is causng the glitch. It seems to show up randomly. Maybe 1 out of 10 times I'll get the glitch version, the rest of the time I get the normal version.

I'm creating these icons by loading an image file. And then drawing 25% black over the icon. My code looks like this:

    
	NSImage *fileIcon = [NSImage imageNamed:@"file"];
	NSImage *darkenedFileIcon = nil;
	NSSize size = [baseImage size];
	NSRect imageRect = {NSZeroPoint, size};
	
	darkenedFileIcon = fileIcon copy] autorelease];

	[darkenedFileIcon lockFocus];
	[[[[[NSColor blackColor] colorWithAlphaComponent:0.25] set];
	NSRectFillUsingOperation(imageRect, NSCompositeSourceAtop);
	[darkenedFileIcon unlockFocus];

	return darkenedFileIcon;


Behind the scenes I'm also caching these icons in a dictionary so I only need to create a darkened version of each icon once. The catching code looks like this. I'm not showing all the code, but I'm pretty sure that retain counts are proper, I'm not experiencing any crashes in any of these cases.

    
	NSValue *lookupKey = [NSValue valueWithPointer:originalImage];
	[darkenedImageCache setObject:lookupKey forKey:darkenedImage];


Has anyone seen this sort of problem before. Does anyone see problems with this code or have suggestions on where I should look to fix the problem?

Thanks,
Jesse

----
*Does the problem only show up on the darkened image (i.e., what happens if you skip the "darkening" code)? Does the white area go away if you resize the window? If you minimize and maximize the window?*

