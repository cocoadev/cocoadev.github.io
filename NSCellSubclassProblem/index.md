---
layout: page
title: NSCellSubclassProblem
---

Hi, I've been working on a small NSCell subclass to display the icon of a given file path, along with the filename in bold and the actual filepath below in light gray.  Everything works fine until I click on the data cell in the table view, at which point it crashes and it's "unable to recover the stack frame".  I've tried conventional debugging here but it has led me nowhere and I don't really know what else to do.  Any help would be appreciated.

(Note: there are some category methods in use here, let me know if any particular one should be posted)

**
Update: I have been investigating this and it appears that my cell is being released (the cell's dealloc method is getting called) when it is clicked on in the table view, even though I am not releasing the cell until the app controller's dealloc method.  Why would this be happening?
**

    
- (id)init
{
	self = [super init];
	
	if (self)
	{
		mPath = nil;
		mIconImage = nil;
		
		fileNameFont = [[NSFont boldSystemFontOfSize:11.0f] retain];
		pathFont = [[NSFont systemFontOfSize:[NSFont smallSystemFontSize]] retain];
	}
	
	return self;
}

- (void)dealloc
{
	[mPath release];
	[mIconImage release];
	
	[fileNameFont release];
	[pathFont release];
	
	[super dealloc];
}

- (void)setFilePath:(NSString*)path
{
	if (mPath != path)
	{
		[mPath release];
		mPath = [path retain];
		
		[mIconImage release];
		mIconImage = [[NSWorkspace sharedWorkspace] iconForFile:path];
		[mIconImage setScalesWhenResized:YES];
		[mIconImage setSize:NSMakeSize(32.0f, 32.0f)];
		[mIconImage setFlipped:YES];
		[mIconImage retain];
	}
}

- (NSImage*)iconImage
{
	return mIconImage;
}

- (NSString*)path
{
	return mPath;
}

- (NSString*)fileName
{
	return [mPath lastPathComponent];
}

- (void)drawInteriorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{	
	NSSize imageSize = [mIconImage size];
	
	NSPoint imagePoint;
	imagePoint.x = 10.0f;
	imagePoint.y = (cellFrame.size.height / 2) - (imageSize.height / 2);
	
	[mIconImage drawAtPoint:imagePoint fromRect:[mIconImage drawingRect] operation:NSCompositeSourceOver fraction:1.0];
	
	NSString* path = [self path];
	NSString* filename = [self fileName];
	
	NSColor* filenameColor = [NSColor blackColor];
	NSColor* pathColor = [NSColor lightGrayColor];
	
	[filename drawAtPoint:NSMakePoint(52.0f, 10.0f) withAttributes:[fileNameFont drawingAttributesWithColor:filenameColor]];
	[path drawAtPoint:NSMakePoint(52.0f, cellFrame.size.height - 25.0f) withAttributes:[pathFont drawingAttributesWithColor:pathColor]];
}


----
See MemoryManagement. When you assign an ivar to a class factory method, you must retain it.
----
Code has been updated to the above suggestion, but the crash has not changed.

----
You need to implement NSCopying. Cells get copied a lot and the default implementation will completely break the memory management on your own ivars, so overriding it is mandatory.

If that still doesn't work, try NSZombieEnabled. As a supplement or alternative, override     -dealloc and put a breakpoint on it so you can see where and why it's getting called.
----
OK, thanks, implementing NSCopying have fixed the problem.  For anyone who is curious, here is the copyWithZone: implementation:
    
- (id)copyWithZone:(NSZone *)zone
{
	JLFileCell* cellCopy = [[JLFileCell allocWithZone:zone] init];
	
	[cellCopy setFilePath:[self path]];
	
	return cellCopy;
}


The fruit of my labors: http://yetanothersite.com/joel/cell.png

----
It's good that it works, but this copy method is somewhat incomplete. You don't call your superclass's copy method, but NSCell does implement NSCopying, so you're failing to copy any attributes that NSCell has. This may not matter in this case, but you never know when this sort of thing may bite you. Unfortunately, integrating a superclass's copy method isn't always simple. There are links to Apple documentation on the NSCopying page which discuss the various approaches on how to do this.
----

Yes, the above will not work.  You've avoided the crashes, which is great, but you're still going to see inexplicable behavior due to not copying the cell correctly.  

Try this:

    
- (id)copyWithZone:(NSZone *)zone
{
	JLFileCell* cellCopy = [super copyWithZone:zone];

	cellCopy->filePath = [filePath copy];
	
	return cellCopy;
}


You cannot just call your accessor due to NSCell's use of NSCopyObject.  See http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/ImplementCopy.html#//apple_ref/doc/uid/20000049-997407 .
----
Thanks everyone who replied, this cell subclass has proved to be a huge learning experience for me.

