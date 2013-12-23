---
layout: page
title: DroppingFileInWell
---

I'm trying to add file dropping support to a custom NSImageView subclass.

    -(void)awakeFromNib
{
	NSArray *draggedTypeArray = [NSArray arrayWithObjects:NSFilenamesPboardType, nil];
	[self registerForDraggedTypes:draggedTypeArray];
}

- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender 
{
	return NSDragOperationMove;
}

But the above code does not change the cursor when dragging a file over the well (implying that it is not accepting the drag) - what am I doing wrong�?
----
You could try switching your draggedTypeArray to     [NSImage imagePasteboardTypes]...

*But I need to support file drops.*

I meant in addition...so I should have said something like     [[NSImage imagePasteboardTypes] arrayByAddingObject:NSFilenamesPboardType]; (sorry). It's also possible that the file is automatically converted, but now that I think about it...no, probably not. However, there are other NSDraggingDestination methods you should probably implement; perhaps     draggingUpdated: could help you here.

----

Sending your image view subclass a setEditable:YES message should be enough for it to accept drops: (edit: accept and set its image to the dropped image, that is)
    
[imageView setEditable:YES]

EnglaBenny


----

Here's a full thing. Addition of delegate methods is left to the reader as an exercise :)


    
//
//  FileDropView.h
//  Packager
//
//  Created by Seth Willits on 5/24/05.
//  Copyright 2005 Freak Software. All rights reserved.
//

#import <Cocoa/Cocoa.h>


@interface FileDropView : NSImageView {
	IBOutlet id delegate;
	BOOL highlight;
	NSString * mFilePath;
}


@end



    
//
//  FileDropView.m
//  Packager
//
//  Created by Seth Willits on 5/24/05.
//  Copyright 2005 Freak Software. All rights reserved.
//

#import "FileDropView.h"


@implementation FileDropView



- (id)initWithCoder:(NSCoder *)coder
{
	self = [super initWithCoder:coder];
	if (self) {
		[self registerForDraggedTypes:[NSArray arrayWithObjects:NSFilenamesPboardType, nil]];
	}
	return self;
}


- (id)initWithFrame:(NSRect)frame
{
	self = [super initWithFrame:frame];
	if (self) {
		[self registerForDraggedTypes:[NSArray arrayWithObjects:NSFilenamesPboardType, nil]];
	}
	return self;
}



- (void)setFile:(NSString *)path
{
	if (mFilePath) {
		[mFilePath release];
	}
	
	mFilePath = [[NSString stringWithString:path] retain];
	NSImage * image = [[NSWorkspace sharedWorkspace] iconForFile:mFilePath];
	[image setSize:NSMakeSize(48.0, 48.0)];
	[self setImage:image];
}




- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender
{
	if ([self isEnabled]) {
		highlight = YES;
		[self setNeedsDisplay:YES];
		return NSDragOperationCopy;
	}
	
	return NSDragOperationNone;
}





- (void)draggingExited:(id <NSDraggingInfo>)sender
{
	highlight = NO;
	[self setNeedsDisplay:YES];
}



///////////////////////////////////////////////////////////
//	Draw method is overridden to do drop highlighing
///////////////////////////////////////////////////////////

- (void)drawRect:(NSRect)rect
{
	// Draw the normal frame first
	[super drawRect:rect];
	
	// Then do the highlighting
	if (highlight) {
		[[NSColor grayColor] set];
		[NSBezierPath setDefaultLineWidth:5];
		[NSBezierPath strokeRect:rect];
	}
}



- (BOOL)prepareForDragOperation:(id <NSDraggingInfo>)sender
{
	highlight = NO;
	[self setNeedsDisplay:YES];
	return YES;
} 



- (BOOL)performDragOperation:(id <NSDraggingInfo>)info
{
	NSPasteboard *pboard = [info draggingPasteboard];
	
	
	// Dragging Filenames From Finder or the List
	if (pboard types] containsObject:[[NSFilenamesPboardType]) {
		
		NSArray * files = [pboard propertyListForType:NSFilenamesPboardType];
		NSEnumerator * enumerator = [files objectEnumerator];
		NSString * filePath;
		
		// Get the First File
		if (filePath = [enumerator nextObject]) {
			[self setFile:filePath];
		}
		
		return YES;
	}
	
	return NO;
}




@end



-- Seth Willits

----

DroppingScreamingChildIntoWell  ... *If someone fills in this stub, I'll toss *them* into a well.*

