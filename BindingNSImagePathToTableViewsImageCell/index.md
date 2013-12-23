---
layout: page
title: BindingNSImagePathToTableViewsImageCell
---

Hello

I'm trying to bind a NSTableView's NSImageCell via Cocoa Bindings. I have a getter method in MyDocument that sets the image path of my image to be a tiff icon file that exists at the application bundle:

    

- (NSString *)cellImagePath {
	
	NSLog([[[NSBundle mainBundle] bundlePath] stringByAppendingString:@"/Contents/Resources/cellImage.tif"]);
	return [[[NSBundle mainBundle] bundlePath] stringByAppendingString:@"/Contents/Resources/cellImage.tif"];

}



In Interface builder, I have the imageCell bound to File's Owner (MyDocument) and the Model Key Path is the name of the getter method (cellImagePath). Running the code I don't get any error and actually the method is fired (I can see that by the log that is created). But no icon appear. What I am missing?

----
I'm assuming that the cell uses - (id)initWithContentsOfFile:(NSString *)filename to load an image at a specific path, then loads that into the cell. But because the images that you are loading are already in your app bundle you might be better off using + (id)imageNamed:(NSString *)name in your getter method and returning an image rather than a string. Then your table's image cell can use the image directly rather than having to allocate an NSImage and load it from path before displaying.

    

- (NSImage *)cellImage {
	
	NSLog(@"imageReturned");
	return [NSImage imageNamed:@"cellImage"];

}



However if you must have a path returned then you might not be allocating your NSImage correctly. Your NSImage object should look like this:

    
NSImage *myCellImage = [[NSImage alloc] initWithContentsOfFile:cellImagePath];


----
Just found the error - here's the catch:

I was binding the ImageCell to the results of my getter method, and not the corresponding NSTableColumn. doing that, the image loads correctly.

----

Can you explain that?  What, finally, is the complete keypath you had to bind?

----

Sure. The steps to bind an image to a NSImageCell in a NSTableView are:



*On Interface Builder, select the desired NSTableColumn and be sure you're selecting it, and not the corresponding NSImageCell;

*On the Inspector (Bindings) set the valuePath for the NSTableColumn to be:



*Bind to: in my case, the file's owner (MyDocument), which is the class where I'll declare the getter method for the image path

*Controller Key: nothing there

*Model Key Path: <code>cellImagePath</code> (this is the name of the method I have on my MyDocument class that returns the path for the image)





Now, in Xcode, create a class that returns the image path. In my case:

    
- (NSString *)cellImagePath {

	return [[[NSBundle mainBundle] bundlePath] stringByAppendingString:@"/Contents/Resources/cellImage.tif"];

}


That should work.

