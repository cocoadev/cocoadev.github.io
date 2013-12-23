---
layout: page
title: ParsingSafariFaviconCache
---

I had reason recently to want to get inside Safari's favicon cache but I couldn't find any information about how to do it.  I came up with a method that could probably be greatly improved, especially since I was (am) utterly unfamiliar with the format that Apple stores them in.  The result seems hackish to me but it works.  I hope someone can use this and that many more can improve it or provide a better method.

    
// Assume path is a path to ~/Library/Safari/Icons/XX/YY/long-name.cache
NSData *dat = [NSData dataWithContentsOfFile:path];
	
// Initialize and create a simple byte array
unsigned char data[ [dat length] ];
[dat getBytes:data];

// Init variables.  These could be moved up so I could use j when initializing 
// my byte array and make one less call, but I don't think it is too costly.
int i, j = [dat length], icostart;
NSString *sitePath = [NSString string];
NSData *iconData = [NSData data];

// Iterate through the byte array
for (i=0; i < j; i++)
{
	if (data[i] == 'h' && data[i+1] == 't' && data[i+2] == 't') //found start of ico path ("http://...")
		icostart = i;
	if (data[i] == '.' && data[i+1] == 'i' && data[i+2] == 'c') //found ico path end ("... .ico")
	{
		i += 4; // Move i to grab the rest of the path

		// I suppose I could use stringWithUTF8String but this method is left over from testing.
		sitePath = [NSString stringWithFormat:@"%s", 
			dat subdataWithRange:[[NSMakeRange(icostart, i - icostart)] bytes]];
		NSURL *url = [NSURL URLWithString:sitePath];  // A quick URL just to put the host into the icoPath
		sitePath = [url host];
	}
	if (data[i] == 'M' && data[i+1] == 'M' && data[i+2] == 0) //found start of icon
	{
		iconData = [dat subdataWithRange:NSMakeRange(i, j - i)]; //Data goes to end of file.
		i = j; //Jump out
	}	
}


There's probably a lot that can be improved.  This is tossed up immediately after testing and messing around with it so a good portion of it is left over from various debugging things (like stringWithFormat: instead of stringWithUTF8String: and setting i to j to jump out of the loop).

Note that the iconData is held in TIFF format, not .ico.  Also, it appears to be scaled to 16x16.  I know some favicons are larger, but Apple apparently scales them for the url field and bookmarks lists.

Any comments are greatly appreciated.

