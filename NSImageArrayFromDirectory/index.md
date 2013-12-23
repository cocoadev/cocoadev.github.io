---
layout: page
title: NSImageArrayFromDirectory
---


This following code works by having a folder of images drag into the project file of XCODE.  The problem is that it will always be limited to those files unless i recompile and build the code if i want to change the images.  Is there a way so that i can grab image files from a directory without referencing it to the project file and recompiling and build the application?

    
     NSArray 	*imageBundlePathArray 	= [[NSBundle mainBundle] pathsForResourcesOfType:@"jpg" inDirectory:nil];
    // iterate over all the jpeg images in our bundle and store
    // them in an NSImage array for later use
    for (i=0; i<[imageBundlePathArray count]; ++i)
    {
        NSURL *fileUrl = [NSURL fileURLWithPath:[imageBundlePathArray objectAtIndex:i]];        
        NSImage *anImage = [[NSImage alloc] initWithContentsOfURL:fileUrl];    

        [imageArray addObject:anImage];

        [anImage release];
    }



Use NSFileManager to iterate over the contents of the directory, and then use a loop similar to what you already have to load the image.

By the way, it is illegal to call init twice, and your image-loading code was generally broken. I've fixed it.

----

I modified the code to 
    
    NSString *file;
    NSString *imageDir = @"/Users/hoboy/Desktop/CocoaCreateMovie/images";
    NSDirectoryEnumerator *dirEnum = [[NSFileManager defaultManager] enumeratorAtPath: imageDir];

    // iterate over all the jpeg images in our bundle and store
    // them in an NSImage array for later use
    while (file = [dirEnum nextObject])
    {
        NSImage *anImage = [[NSImage alloc] init];
	if(file pathExtension] isEqualToString: @"jpg"])
	{
            [imageArray addObject:[anImage initWithContentsOfFile:file;

            [anImage release];
	}
    }


It compiles okay...but when i run the program it termniates straight away if an error.  I think it is not reading it correctly into the array. Anyone has any ideas?

robbieduncan: It's still wrong to call init twice (i.e. you alloc init the image then initWithContentsOfFile on the same object).  What is the error that you are receiving?

*This is bad form. Separate out the commands and only add/release the image if it exists like so:*

    
     while (file = [dirEnum nextObject])
    {
        NSImage *anImage;
	if(file pathExtension] isEqualToString: @"jpg"])
	{
            anImage = [[[[NSImage alloc] initWithContentsOfFile:file];
            if (anImage)
            {
                [imageArray addObject:anImage];
                [anImage release];
            }
	}
   }

----
This is the error i get..
    

[Session started at 2005-07-12 12:06:31 +0800.]
2005-07-12 12:06:31.923 CocoaCreateMovie[370] *** -[NSView initWithContentsOfFile:]: selector not recognized [self = 0x350890]
2005-07-12 12:06:31.924 CocoaCreateMovie[370] An uncaught exception was raised
2005-07-12 12:06:31.924 CocoaCreateMovie[370] *** -[NSView initWithContentsOfFile:]: selector not recognized [self = 0x350890]
2005-07-12 12:06:31.924 CocoaCreateMovie[370] *** Uncaught exception: <NSInvalidArgumentException> *** -[NSView initWithContentsOfFile:]: selector not recognized [self = 0x350890]

CocoaCreateMovie has exited due to signal 5 (SIGTRAP).


*    alloc is a class method; you should be saying     anImage = [[NSImage alloc] initWithContentsOfFile:file]; The reason for the NSView is that before being set,     anImage just points to some random piece of memory, which in this case happens to be an NSView. --JediKnil*

----
That being fix..i get the following error..
    
[Session started at 2005-07-12 12:46:39 +0800.]
2005-07-12 12:46:41.588 CocoaCreateMovie[414] An uncaught exception was raised
2005-07-12 12:46:41.589 CocoaCreateMovie[414] *** -[NSCFArray objectAtIndex:]: index (0) beyond bounds (0)
2005-07-12 12:46:41.589 CocoaCreateMovie[414] *** Uncaught exception: <NSRangeException> *** -[NSCFArray objectAtIndex:]: index (0) beyond bounds (0)

CocoaCreateMovie has exited due to signal 5 (SIGTRAP).


Where do you get that exception, and what is the relevant code?

----
The last line of the following code is where the exception occurs...I think there is nothing in the array.. but i can't seem to figure how why.  If you can help that would be great. Thanks
    
    NSSize  imageSize;
    NSString *file;
    NSString *imageDir = @"/Users/johnho/Desktop/CocoaCreateMovie/images";
    NSDirectoryEnumerator *dirEnum = [[NSFileManager defaultManager] enumeratorAtPath: imageDir];

     while (file = [dirEnum nextObject])
    {
        NSImage *anImage;
	    if(file pathExtension] isEqualToString: @"jpg"])
	    {
            anImage = [[[[NSImage alloc] initWithContentsOfFile:file];
            if (anImage)
            {
                [imageArray addObject:anImage];
                [anImage release];
            }
	    }
    }
    // pass our controller object instance to our C code so it may
    // be used to call methods in this class
    setObjCObject(self);
    // we'll size our movie view to that of the
    // first image we find in our bundle
    imageSize = imageArray objectAtIndex:0] size];


**Poster: You're going to have to be more detailed than this. We cannot help you if you're going to be vague. You must tell us on what line the exception is occurring, etc. DETAILS. Nobody will be able to (be willing to) help you if we have to keep prodding you to provide useful info. As to the problem above, if you think nothing's in the array (which is what the exception does indicate), then that's a solid clue that you should perhaps check to see if there's anything in the array before trying to operate on things in the array. How do you know there's a valid object at index 0 if you don't check to see how many objects there are? Use     [imageArray count]. Also, do you intend only to get the first object in the array? If you use an [[NSEnumerator, you wouldn't even have to explicitly check for the array's count. Just use an enumerator - if the array is empty, nothing will happen in the 'do-this-with-every-object-in-the-array' loop.**

You need to learn to use the debugger. This site is not a substitute for gdb, no matter how much it may appear to be a slow, interactive debugger from time to time. If you don't know how to use it, then go learn it right away! Knowing how to use gdb is so useful that I can't even properly state how useful it is. Trying to program anything in ObjC of any significant size without knowing how to use gdb is like a carpenter trying to build a house without knowing how to use a tape measure.

*Or rather like carrying the boards to be measured over to your neighbor's house and asking that he do it for you, then carrying the boards back over to the building site ..... you see the inefficiency here I hope. ;-)*

----

The code below seems to work well enough and combines two examples provided to date. Be aware that running this code when using 50 large astronomical images consumes ~1GB of RAM.

    
    NSMutableArray * imageArray = [[NSMutableArray alloc] init];
    NSEnumerator * imageBundlePathEnumerator = [[[NSBundle mainBundle] pathsForResourcesOfType: @"jpg" inDirectory: nil] objectEnumerator];
    NSString * imageBundlePath = nil;

    while (imageBundlePath = [imageBundlePathEnumerator nextObject])
    {
        [imageArray addObject: [[[NSImage alloc] initWithContentsOfFile: imageBundlePath] autorelease]];
    }

    NSLog(@"Number of images loaded : %d", [imageArray count]);

    NSImage * anImage = nil;
    NSEnumerator * imageEnumerator = [imageArray objectEnumerator];

    while (anImage = [imageEnumerator nextObject])
    {
        NSLog(@"image = %@", anImage);
    }

