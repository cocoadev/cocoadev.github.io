---
layout: page
title: ProblemWithClass
---



I know we haven't finished with HowDoIDoRadioButtonsWithRegularButtons, but this has driven me crazy all day long. I can't seem to get this code to work.

AmpersandArt.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Data Interface
----------------------------------
*/

#import <Cocoa/Cocoa.h>


@interface AmpersandArt : NSObject {
	NSMutableArray *layers;
}
- (NSMutableArray *)layers;
@end


AmpersandArt.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Data Implementation
----------------------------------
*/

#import "AmpersandArt.h"

@class AmpersandArtLayer;

@implementation AmpersandArt

// init:  creates a new art
- (id)init
{
	self = [super init];
	if (self) {
		layers = [[NSMutableArray alloc] init];
		[layers addObject:[[AmpersandArtLayer alloc] init]];
	}
	return self;
}

// layers:  gets the layers of the picture
- (NSMutableArray *)layers
{
	return layers;
}

@end


AmpersandArtLayer.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Layer Interface
----------------------------------
*/

#import  <Cocoa/Cocoa.h>

@class AmpersandArtObject;

@interface AmpersandArtLayer : NSObject {
	NSMutableArray *objects;
	BOOL visible;
}
- (int)addObject:(AmpersandArtObject *)object;
- (void)removeObjectAtIndex:(int)whatIndex;
- (NSMutableArray *)objects;
- (void)setVisible:(BOOL)isVisible;
- (BOOL)visibility;
@end


AmpersandArtLayer.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Layer Implementation
----------------------------------
*/

#import "AmpersandArtLayer.h"


@implementation AmpersandArtLayer

// init:  creates the layer
- (id)init
{
	self = [super init];
	if (self) {
		[self setVisible:YES];
		objects = [[NSMutableArray alloc] init];
		if (objects == nil)
			NSLog(@"objects==nil");
	}
	return self;
}

// dealloc:  destroys the layer
- (void)dealloc
{
	[objects release];
	[super dealloc];
}

// addObject:  adds an object and returns the index of the object in the object array
- (int)addObject:(AmpersandArtObject *)object
{
	[objects addObject:object];
	return [objects count] - 1;
}

// removeObjectAtIndex:  removes the object at a given index
- (void)removeObjectAtIndex:(int)whatIndex
{
	[objects removeObjectAtIndex:whatIndex];
}

// objects:  gets the objects array
- (NSMutableArray *)objects
{
	return objects;
}

// setVisible:  sets the visibility of the layer
- (void)setVisible:(BOOL)isVisible
{
	visible = isVisible;
}

// visibility:  gets the visibility of the layer
- (BOOL)visibility
{
	return visible;
}

@end


AmpersandArtObject.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Object Interface
----------------------------------
*/

#import <Cocoa/Cocoa.h>

typedef enum _Object_Type {
	POINT,
	BOX
} ObjectType;

const NSSize HAS_NO_SIZE = {0, 0};

@interface AmpersandArtObject : NSObject {
	ObjectType objectType;
	NSPoint origin;
	NSSize size;
	NSColor *fgColor, *bgColor;
	NSMutableArray *data;
}
- (AmpersandArtObject *)initObjectOfType:(ObjectType)object at:(NSPoint)p ofSize:(NSSize)s;
- (void)setType:(ObjectType)objtype;
- (ObjectType)type;
- (void)setOrigin:(NSPoint)anOrigin;
- (NSPoint)origin;
- (void)setSize:(NSSize)aSize;
- (NSSize)size;
- (void)setFgColor:(NSColor *)color;
- (NSColor *)fgColor;
- (void)setBgColor:(NSColor *)color;
- (NSColor *)bgColor;
@end


AmpersandArtObject.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Ampersand Art Object Implementation
----------------------------------
*/

#import "AmpersandArtObject.h"


@implementation AmpersandArtObject

// init:  make a noew object
- (id)init
{
	self = [super init];
	if (self)
		return [self initObjectOfType:POINT at:NSMakePoint(0,0) ofSize:HAS_NO_SIZE];
	return nil;
}

// initObjectOfType at ofSize:  creates a new object with specified type
- (AmpersandArtObject *)initObjectOfType:(ObjectType)object at:(NSPoint)p ofSize:(NSSize)s
{
	if (self == nil)
		self = [self init];
	if (self == nil)
		return nil;
	[self setType:object];
	[self setOrigin:p];
	[self setSize:s];
	[self setFgColor:[NSColor blackColor]];
	[self setBgColor:[NSColor whiteColor]];
	return self;
}

// setType:  sets the object's type
- (void)setType:(ObjectType)objtype
{
	objectType = objtype;
}

// type:  gets the object's type
- (ObjectType)type
{
	return objectType;
}

// setOrigin:  sets the object's origin
- (void)setOrigin:(NSPoint)anOrigin
{
	origin = anOrigin;
}

// origin:  gets the object's origin
- (NSPoint)origin
{
	return origin;
}

// setSize:  sets the object's size
- (void)setSize:(NSSize)aSize
{
	size = aSize;
}

// size:  get the object's size
- (NSSize)size
{
	return size;
}

// setFgColor:  sets the object's foreground color
- (void)setFgColor:(NSColor *)color
{
	fgColor = color;
}

// fgColor:  gets the object's foreground color
- (NSColor *)fgColor
{
	return fgColor;
}

// setBgColor:  sets the object's background color
- (void)setBgColor:(NSColor *)color
{
	bgColor = color;
}

// bgColor:  gets the object's background color
- (NSColor *)bgColor
{
	return bgColor;
}

@end


ArtView.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Art Editor Interface
----------------------------------
*/

#import <Cocoa/Cocoa.h>

@class AmpersandArt;
@class AmpersandArtLayer;
@class AmpersandArtObject;

@interface ArtEditor : NSView {
	AmpersandArt *art;
}
- (void)handleSetArt:(NSNotification *)note;
- (AmpersandArt *)art;
@end


ArtView.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Art Editor Implementation
----------------------------------
*/

#import "ArtEditor.h"
#import "AmpersandArtObject.h"
#import "Notifications.h"

@class AmpersandArt;
@class AmpersandArtLayer;
@class AmpersandArtObject;

@implementation ArtEditor

/* initWithFrame:  creates a new ArtEditor with a preset frame */
- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
		[self setFrame:NSMakeRect(frame.origin.x,frame.origin.y,1000,1000)];
		[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleSetArt:) name:SetPicture_ object:nil];
	}
    return self;
}

// dealloc:  destroys the object
- (void)dealloc
{
	[[NSNotificationCenter defaultCenter] removeObserver:self];
	[super dealloc];
}

/* drawRect:  draws the frame */
- (void)drawRect:(NSRect)Rect
{
	NSEnumerator *enumerator, *lenumerator;
	AmpersandArtLayer *l;
	AmpersandArtObject *obj;
	
	if (art == nil) {
		NSLog(@"nil");
		return;
	}
	NSLog(@"starting redraw");
	[[NSColor whiteColor] setFill];
	NSRectFill([self frame]);
	enumerator = [self art] layers] objectEnumerator];
	while ((l = [enumerator nextObject]) != nil) {
		lenumerator = [[l objects] objectEnumerator];
		while ((obj = [lenumerator nextObject]) != nil)
			switch ([obj type]) {
			case POINT:
				[[NSLog(@"%@ is point", obj);
				obj fgColor] setFill];
				[[NSRectFill(NSMakeRect([obj origin].x, [obj origin].y, 1, 1));
				break;
			case BOX:
				NSLog(@"%@ is box", obj);
				obj fgColor] setFill];
				[[NSRectFill(NSMakeRect([obj origin].x, [obj origin].y, [obj size].width, [obj size].height));
				break;
			}
		[lenumerator release];
	}
	[enumerator release];
}

/* isFlipped:  will always return YES to say that (0,0) is top-left corner */
- (BOOL)isFlipped
{
	return YES;
}

// handleSetArt:  notification handler when art is set
- (void)handleSetArt:(NSNotification *)note
{
	NSLog(@"set art");
	art = [note object] art] retain];
}

// mouseDown:  when the mouse is down on the view, do this
- (void)mouseDown:([[NSEvent *)event
{
	NSMutableArray *arr;
	int i;
	AmpersandArtLayer *l;
	AmpersandArtObject *o;

	NSLog(@"we're here");
	NSLog(@"getting layer");
	arr = self art] layers];
	[[NSLog(@"%@", arr);
	i = [arr count] - 1;
	NSLog(@"%d", i);
	l = [arr objectAtIndex:i];
	NSLog(@"making object");
	o = [[AmpersandArtObject alloc] initObjectOfType:POINT at:[event locationInWindow] ofSize:HAS_NO_SIZE];
	NSLog(@"adding object");
	[l addObject:o];
	NSLog(@"cleaning up");
	[o release];
	NSLog(@"redrawing");
	[self setNeedsDisplay:TRUE];
}

// art:  get the art object
- (AmpersandArt *)art
{
	return art;
}

@end


MyDocument.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Document Handler Interface
----------------------------------
*/


#import <Cocoa/Cocoa.h>

@class AmpersandArt;

@interface MyDocument : NSDocument {
	AmpersandArt *art;
}
- (AmpersandArt *)art;
@end


MyDocument.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Document Handler Implementation
----------------------------------
*/

#import "MyDocument.h"
#import "Notifications.h"

@implementation MyDocument

// init:  creates a new document
- (id)init
{
    self = [super init];
    if (self)
        art = [[AmpersandArt alloc] init];
    return self;
}

// windowNibName:  returns the nib name
- (NSString *)windowNibName
{
    // Override returning the nib file name of the document
    // If you need to use a subclass of NSWindowController or if your document supports multiple NSWindowControllers, you should remove this method and override -makeWindowControllers instead.
    return @"MyDocument";
}

// windowControllerDidLoadNib:  performs an action when the document has loaded
- (void)windowControllerDidLoadNib:(NSWindowController *)aController
{
    [super windowControllerDidLoadNib:aController];
	[[NSNotificationCenter defaultCenter] postNotification:[NSNotification notificationWithName:SetPicture_ object:self]];
}

// dataRepresentationOfType:  saves the document
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    // Insert code here to write your document from the given data.  You can also choose to override -fileWrapperRepresentationOfType: or -writeToFile:ofType: instead.
    
    // For applications targeted for Tiger or later systems, you should use the new Tiger API -dataOfType:error:.  In this case you can also choose to override -writeToURL:ofType:error:, -fileWrapperOfType:error:, or -writeToURL:ofType:forSaveOperation:originalContentsURL:error: instead.

    return nil;
}

// loadDataRepresentation:  opens the document
- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    // Insert code here to read your document from the given data.  You can also choose to override -loadFileWrapperRepresentation:ofType: or -readFromFile:ofType: instead.
    
    // For applications targeted for Tiger or later systems, you should use the new Tiger API readFromData:ofType:error:.  In this case you can also choose to override -readFromURL:ofType:error: or -readFromFileWrapper:ofType:error: instead.
    
    return YES;
}

// art:  gets the art object
- (AmpersandArt *)art
{
	return art;
}

@end


Notifications.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Notification Names
----------------------------------
*/

#import <Foundation/Foundation.h>

NSString *SetPicture_ = @"SaratogaNOTESetPicture";
NSString *GetPicture_ = @"SaratogaNOTEGetPicture";
NSString *SetTool_ = @"SaratogaNOTESetTool";
NSString *GetTool_ = @"SaratogaNOTEGetTool";


When I build a project containing the above 10 files, this is the Errors and Warnings list that I get:

    
ArtEditor.m:53: (Messages without a matching method signature will be assumed to return 'id' and accept '...' as arguments.)
ArtEditor.m:53: warning: no '-layers' method found
ArtEditor.m:55: warning: no '-objects' method found
ArtEditor.m:97: warning: no '-layers' method found


which causes Saratoga to crash and burn.

What I don't understand is that     layers and     objects are **clearly** defined in my code, and casting does **nothing** to solve the problem. What am I or what is gcc doing wrong? - PietroGagliardi

----

Did you #import the headers in which these methods are defined?

----
Thanks, but now I still have a crash.

ArtEdit.h:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Art Editor Interface
----------------------------------
*/

#import <Cocoa/Cocoa.h>
#import "AmpersandArt.h"
#import "AmpersandArtLayer.h"
#import "AmpersandArtObject.h"

@class AmpersandArt;
@class AmpersandArtLayer;
@class AmpersandArtObject;

@interface ArtEditor : NSView {
	AmpersandArt *art;
}
- (void)handleSetArt:(NSNotification *)note;
- (AmpersandArt *)art;
@end


ArtEdit.m:
    
/*
----------------------------------
Saratoga v1.0
----------------------------------
AMPERSAND LABORATORIES
----------------------------------
Art Editor Implementation
----------------------------------
*/

#import "ArtEditor.h"
#import "AmpersandArtObject.h"
#import "Notifications.h"

@implementation ArtEditor

/* initWithFrame:  creates a new ArtEditor with a preset frame */
- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
		[self setFrame:NSMakeRect(frame.origin.x,frame.origin.y,1000,1000)];
		[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleSetArt:) name:SetPicture_ object:nil];
	}
    return self;
}

// dealloc:  destroys the object
- (void)dealloc
{
	[[NSNotificationCenter defaultCenter] removeObserver:self];
	[super dealloc];
}

/* drawRect:  draws the frame */
- (void)drawRect:(NSRect)Rect
{
	NSEnumerator *enumerator, *lenumerator;
	AmpersandArtLayer *l;
	NSMutableArray *lay;
	AmpersandArtObject *obj;
	
	if (art == nil) {
		NSLog(@"nil");
		return;
	}
	NSLog(@"starting redraw");
	NSLog(@"setting white for bg");
	[[NSColor whiteColor] setFill];
	NSLog(@"filling rect");
	NSRectFill([self frame]);
	NSLog(@"testing");
	if ([self art] == nil) {
		NSLog(@"nil art");
		return;
	}
	if (self art] layers] == nil) {
		[[NSLog(@"nil layers");
		return;
	}
	NSLog(@"setting layers");
	lay = self art] layers];
	[[NSLog(@"setting enumerator");
// ------------------------------------------
// THE BUG IS HERE
// ERC_BAD_ADDRESS
	enumerator = [lay objectEnumerator];
// ------------------------------------------
	while ((l = [enumerator nextObject]) != nil) {
		if ([l visibility] == NO)
			continue;
		if (l objects] count] == 0)
			continue;
		lenumerator = [[l objects] objectEnumerator];
		if (lenumerator == nil) {
			[[NSLog(@"nil objects");
			return;
		}
		while ((obj = [lenumerator nextObject]) != nil)
			switch ([obj type]) {
			case POINT:
				NSLog(@"%@ is point", obj);
				obj fgColor] setFill];
				[[NSRectFill(NSMakeRect([obj origin].x, [obj origin].y, 1, 1));
				break;
			case BOX:
				NSLog(@"%@ is box", obj);
				obj fgColor] setFill];
				[[NSRectFill(NSMakeRect([obj origin].x, [obj origin].y, [obj size].width, [obj size].height));
				break;
			}
		[lenumerator release];
	}
	[enumerator release];
}

/* isFlipped:  will always return YES to say that (0,0) is top-left corner */
- (BOOL)isFlipped
{
	return YES;
}

// handleSetArt:  notification handler when art is set
- (void)handleSetArt:(NSNotification *)note
{
	NSLog(@"set art");
	art = [note object] art] retain];
}

// mouseDown:  when the mouse is down on the view, do this
- (void)mouseDown:([[NSEvent *)event
{
	NSMutableArray *arr;
	int i;
	AmpersandArtLayer *l;
	AmpersandArtObject *o;

	NSLog(@"we're here");
	NSLog(@"getting layer");
	arr = self art] layers];
	[[NSLog(@"%@", arr);
	i = [arr count] - 1;
	NSLog(@"%d", i);
	l = [arr objectAtIndex:i];
	NSLog(@"making object");
	o = [[AmpersandArtObject alloc] initObjectOfType:POINT at:[event locationInWindow] ofSize:HAS_NO_SIZE];
	NSLog(@"adding object");
	[l addObject:o];
	NSLog(@"cleaning up");
	[o release];
	NSLog(@"redrawing");
	[self setNeedsDisplay:TRUE];
}

// art:  get the art object
- (AmpersandArt *)art
{
	return art;
}

@end


How can an enumerator be at a bad address? - PietroGagliardi

----

A few tips here:


* Your MemoryManagement is broken in many ways. Read up on it and fix your code. This will probably stop the crash.
* Your page title is terrible. Think of another one. Alternatively, this is the sort of MailingListMode problem-solving which probably won't become a useful wiki page and as such may be more suitable to a venue such as the cocoa-dev mailing list.
* This page contains way too much code. Try to only post the code that's relevant to the problem. If you can't do that (and here you may not be able to), then post a .zip of your project instead of posting the full contents of a dozen different files.
* Clearly ask your question at the top of the page. For this page, post the exact errors you're getting, including the warnings and the crash. Indicate where the crash happens in your code if you know. After that, post code or a link to a .zip.
* Remember, the easier you make our job, the better help you'll get.


----
I'm in the process of replacing the above with a system based on a NSTableView and an NSArrayController, which I hope will be a lot cleaner and less crash-prone. I wanted to hold off on the interface design until the above was fully implemented, but now I realize it is a bad idea. - PietroGagliardi

----

Have a look at /Developer/Examples/AppKit/Sketch I think it's what your looking for (i.e a simple object oriented drawing application).

