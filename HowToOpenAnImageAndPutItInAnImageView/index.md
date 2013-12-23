---
layout: page
title: HowToOpenAnImageAndPutItInAnImageView
---

This article shows you the code that will open an image file (jpg, gif, pdf, etc) in the finder, assign the image to an NSImage, and then put this image into an NSImageView.

In addition to this code, you need to create an outlet for an NSImageView (named myImageView) in your header file.  You also need to put this code in a method that you attach to perhaps the "Open" item in the "File" menu.

    
//Declare op as a pointer to an NSOpenPanel.
NSOpenPanel *op;

//Create, initilize, and assign an NSOpenPanel to the pointer op.
op = [[NSOpenPanel alloc] init];

//Open an Open panel and record the file the user selected.
[op runModal];

//Assign the image file to the NSImage pointer named image.
NSImage *image=[[NSImage alloc] initWithContentsOfFile: [op filename]]; 

//Put the image into the NSImageView.
 [myImageView setImage:image]; 

//Release the objects you allocated above.
[image release];
[op release];


Back to HowToProgramInOSX

