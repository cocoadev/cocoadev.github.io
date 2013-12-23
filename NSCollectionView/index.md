---
layout: page
title: NSCollectionView
---

NSCollectionView is a grid-like structure which contains views. It's similar to NSMatrix, but for NSView objects rather than NSCell objects. Compatible with Leopard (10.5) onwards. It's possibly used by iPhoto for displaying photo thumbnails.

----

Apple Documentation:

http://developer.apple.com/documentation/Cocoa/Reference/NSCollectionView_Class/Introduction/Introduction.html

Example code:

http://developer.apple.com/samplecode/SourceView/index.html

I'd say the documentation that Apple have provided for using NSCollectionView is currently in a "poor" state. There's a lot of details missing, like how to resize the views within the collection, how selecting and multiply-selecting functions works, dragging to re-arrange, how to switch core animation off, and so forth. I hope we'll be able to build up some tips on this page.

----

The easiest way to create an NSCollectionView is within Interface Builder. If you drag it into your superview, it will create two additional objects within your nib, and connect them up:


* A "collection item" prototype which mediates between your model and the view, of type NSCollectionViewItem.
* A "prototype" which represents the item that is drawn within the NSCollectionView, of type NSView. 


For simple things, all you need to do to use the NSCollectionView is set the 'content' binding of the instance to an array of KVM-compilant objects which are represented by the view. You'll then need to fill your "prototype view" with whatever you want to display. For example, you may want an NSImageView and an NSTextField beneath it. You'll then need to bind the NSImageView and NSTextField subview values to the NSCollectionViewItem. This class has a property "representedObject" which can be used to retrieve a particular object from your 'content'.

If you do this, you'll end up with a program which can display a representation of your content objects, and if you remove or add any, they will appear: core animation is used for shifting the views around the NSCollectionView when this occurs, or when the NSCollectionView view is resized.

----

When binding the collection view's 'content' to an array controller, you probably will also want to bind its 'selectionIndexes' to the same array controller, so you get 'canRemove' for free and potentially more of your UI will be code-free.

----

**How do I visibly indicate something is "selected" in an NSCollectionView?**

This is what I have found out works so far for me. You need to do some extra work in your NSView so that it's possible to show what's selected and what's not. For example:

http://mutablelogic.com/cocoa/IconView.gif

Make sure that the "selectable" property is ticked in your NSCollectionView instance. I have then implemented subclasses of NSCollectionViewItem and the NSView which contain the following code:

    
#import "CollectionViewItem.h"
#import "View.h"

@implementation CollectionViewItem

-(void)setSelected:(BOOL)flag {
  [super setSelected:flag];
  
  // tell the view that it has been selected
  [(View* )[self view] setSelected:flag];
  [(View* )[self view] setNeedsDisplay:YES];
}

@end


    
#import "View.h"

@implementation View

-(void)setSelected:(BOOL)flag {
  m_isSelected = flag;
}

-(BOOL)selected {
  return m_isSelected;
}

-(void)drawRect:(NSRect)rect {
  if([self selected]) {
    [[NSColor grayColor] set];
    NSRectFill([self bounds]);    
  }
  [super drawRect:rect];
}

@end


This draws a grey box around the view when the view is selected. You can download the code for this here:

http://mutablelogic.com/cocoa/IconView.zip (565Kb)

----

Alternatively, simply bind the view's 'selected' property to the collection view item's in your window controller or view controller (or in IB, if you've made a palette), which saves you one subclass and a little work.

Apple's sample code (IconCollection: http://developer.apple.com/samplecode/IconCollection/) uses an NSBox with a fill colour and with its 'transparent' binding bound to the collection view item's 'selected' property, negated. That way when the item in question is selected, the box's transparent property gets turned off, and the fill colour shows.

----

**How do I actually "select" something with my mouse in an NSCollectionView subview?**

There is some additional code in your NSView subclass to actually register the mouse click without allowing subclasses to register the mouse click, which may or may not be useful for you. I'm sending a delegate (which I have connected up to my controller) a message whenever a double click occurs. In my NIB file, I have connected up the Controller with the prototype View's delegate. The "copyWithZone" method is used when NSCollectionView makes new subviews:

    

-(id)copyWithZone:(NSZone *)zone {
  View* theCopy = View allocWithZone:zone] initWithFrame:[self frame;
  [theCopy setDelegate:[self delegate]];
  [theCopy setSelected:[self selected]];  
  return theCopy;
}

-(id)delegate {
  return m_theDelegate;
}

-(void)setDelegate:(id)theDelegate {
  m_theDelegate = theDelegate;
}

-(NSView *)hitTest:(NSPoint)aPoint {
  // don't allow any mouse clicks for subviews in this view
  if(NSPointInRect(aPoint,[self convertRect:[self bounds] toView:[self superview]])) {
    return self;
  } else {
    return nil;    
  }
}

-(void)mouseDown:(NSEvent *)theEvent {
  [super mouseDown:theEvent];

  // check for click count above one, which we assume means it's a double click
  if([theEvent clickCount] > 1) {
    if(self delegate] respondsToSelector:@selector(doubleClick:)]) {
      [[self delegate] doubleClick:self];
    }
  }
}



A more in-depth tutorial about this: http://blog.springenwerk.com/2009/12/double-click-and-nscollectionview.html

----

**How do I change the size of the [[NSCollectionView subviews?**

I might be entirely wrong but I am just setting the minimum and maximum sizes using the NSCollectionView methods. Here is the code to do this, if you're going to subclass NSCollectionView:

    
#import "CollectionView.h"

@implementation CollectionView

-(void)setSubviewSize:(CGFloat)theSubviewSize {
  [self setMaxItemSize:NSMakeSize(theSubviewSize,theSubviewSize)];
  [self setMinItemSize:NSMakeSize(theSubviewSize,theSubviewSize)];
}

@end



It seems to me that the problem with this is that the collection re-arrangement is awfully slow when you do this - is there a better suggestion for resizing the subviews?

- DavidThorpe
----
I was having trouble setting up an NSCollectionView initially, so I wrote a walkthrough of what I did to get it all to work (which certainly would have saved me time had I had it):

*1) Add the collection view to the view in IB
*2) Design your prototype view (the view added when you added the NSCollectionView that is used to display your data items)
*3) Add an array controller to the .nib
*4) Create controller class
*5) Define an array in the controller and make it a property
*6) add in controller class awakeFromNib that allocates NSMutableArray and sets its property to that array.  This is where your stuff to display will go.  You may also want to just have some canned data put into the array here so you can see it work initially.  I just used some NSDictionaries for my test data and then switched over to my real object after getting it working.  
*7) read your controller into IB
*8) instantiate your controller in IB
*9) bind array controller "content array" to your controller array property
*10) add a collectionview IBOutlet to controller, synch nib, connect (not 100% required)
*11) bind NSCollectionView "content" to arraycontroller.arrangedobjects
*12) bind view data items to the CollectionViewItem.representedObject.<whatever>

And that managed to get my canned data displaying. Another problem I had later was to get the NSCollectionView to update when I added items to the array.  Stupid me was using addObject on the NSArray, not the NSArrayController controlling it.  When I switched, all worked great.

Now of course this is really rudimentary but it got the NSCollectionView populating and I was able to progress quickly after managing to figure out this first step.

- Guy Umbright

----

Thanks in part to info gleaned here, my NSCollectionView is up and running. I have the collection view bound to an array controller, which is bound to a Core Data entity. With no fetch request set, it currently displays all existing objects of the appropriate class.

The problem is that my object creation loop can be sort of slow (it depends on network access), and I'd really like to have the collection view items show up in the view as they're created. Instead, they all gracefully fade in together at the end of the loop. I've used this exact same pattern when filling out NSTableViews in the past, and all I had to do to get the tableview to display each new object in real time was to call "[myTableview] superview] superview] displayIfNeeded]" each time through the loop. 

The tableview's superview is an [[NSClipView, and its superview is the NSScrollView, so calling displayIfNeeded on it properly redraws the scroll thumbs as well as the tableview contents. NSCollectionView's superview hierarchy is the same, but calling displayIfNeeded has no apparent effect.

Short of forcing each object creation to happen in a different runloop cycle, any ideas for how to force objects to show up in a collectionview promptly?

----

Like any long-running loop, you should move it to a timer (or a thread) to avoid blocking the GUI. This will not only let the collection view update, but it will also prevent the spinning pizza of death and let the user interact with your program. This last one can be tricky, so be careful to disable any controls or windows which can't handle the intermediate state.

----

The process by which a subview in a collection view is cloned is a quirky one, and while most users probably shouldn't need the details of the implementation, it might be useful to know what's going on under the hood, especially if you're trying to do some varying behavior or subclassing.

Since views don't conform to NSCopying, AppKit uses some tricks to clone the prototype view for an NSCollectionView. It would appear that the steps taken are as follows when     -[NSCollectionView newItemForRepresentedObject:] is called:


*1) A new instance of NSCollectionViewItem is allocated
*2) The prototype view is encoded into an in-memory NSMutableData object using an NSKeyedArchiver
*3) A new copy of the view is then created by decoding it from the same data, using an NSKeyedUnarchiver
*4) This new view is associated with the new prototype item by calling     -[NSCollectionViewItem setView:]
*5) The represented object is associated with the new prototype item by calling     -[NSCollectionViewItem setRepresentedObject:]
*6) The selected state of the view item is set as is appropriate and the view is repositioned into its target frame rectangle
*7) The following connections are manually "fixed":
*7.1) ... if the prototype view class responds to the     target and     setTarget: selectors, the target of the prototype is assigned to the new view
*7.2) ... if the prototype view class responds to the     action and     setAction: selectors, the action of the prototype is assigned to the new view
*7.3) ... if the prototype view class responds to the     delegate and     setDelegate: selectors, the delegate of the prototype is assigned to the new view
*7.4) ... for each exposed binding in the prototype view, the new view is bound to the same object (using     bind:toObject:keyPath:options) with the prototype's observed object, observed key path, and observation options
*7.5) ... this process is then repeated from step 7.1 recursively for all subviews of the prototype view


I think the list above covers everything that goes on. Use this knowledge at your own risk since Apple could change the behavior in a later version, but since the documentation is horrible at the moment, it might be nice to know exactly how the view copy is being accomplished.
----
Is there ANY way I can modify the "NSCollectionView Item View" prototype that gets instantiated in IB when adding a CollectionView to your window? I'd love to give it a red bg color, but it doesn't seem to work. I assigned it a new custom class. Why doesn't it work?

----
In case any body would like a collection view like class for Tiger here's some sample code. CollectionViewForTiger

----

I've found it is possible to replicate the Finder's icon view almost perfectly using NSCollectionView, a basic NSView and bindings. I haven't replicated multiline labels. I'm not sure that is possible without creating a new control.
---- 
 Set the text label to "wrap" in Layout pane of inspector. Then you will be allowed to resize it vertically as well as horizontally, and it will become a multiline label. The downside is that it means you can't select "Truncate Head|Middle|Tail" in the Line Breaks entry, however you can do this programatically instead.
---- 

Here is what I did. Open the basic NSView that is automatically created when you draw a NSCollectionView and:

*1) Draw two boxes, one for the label and one for the icon.
*2) Set the size of the icon one to 64x64. Set the Box Type to custom, and Border Type to line. Set the border colour to selectedControlColor (select 'Color Palettes' in the colour picker and then select the Developer palette). Set the fill colour to alternateSelectedControlColor and set the opacity to 20%. Uncheck Enabled. Change the border width and radius to 2.
*3)Embed an NSImageView within it and set its size to 48x48. Use the alignment buttons to centre it.
*4)Set the size of the label box to something x 20. Set the Box Type to custom, and the Border Type to line. Set the fill colour to alternateSelectedcontrolColor. Change the border width to 0 and the radius to 10.
*5)Embed a label within it and position it. Set the colour to controlTextColor.
*6)Duplicate the label and set the colour to alternativeSelectedControlTextColor. Position it so it directly overlaps the first label. You might want to do this after you've set up the bindings.


Now set up the bindings.

*1) Bind the NSImageView's Value to "Collection View Item" with the path "representedObject.<your icon property>". Uncheck Conditionally Sets Enabled.
*2) Bind the surrounding NSBox's Transparent to "Collection View Item" with the path "selected". Set the Value Transformer to NSNegateBoolean.
*3) Bind the first label's Value to "Collection View Item" with the path "representedObject.<your label property>". Bind Hidden to "Collection View Item" with the path "selected".
*4) Bind the first label's Value to "Collection View Item" with the path "representedObject.<your label property>". Bind Hidden to "Collection View Item" with the path "selected". Set the Value Transformer to NSNegateBoolean.
*5) Bind the surround NSBox's Transparent to "Collection View Item" with the path "selected". Set the Value Transformer to NSNegateBoolean.


Don't forget to set the NSCollectionView as selectable. This works with small numbers of items. I'm not sure if it scales well.

-- Tim Bedford

----

**
How to bind a NSTableView in the prototype view of a NSCollectionView to show a to-many-relationship (e.g. entity "TWO", the name's property is called "name") of the collection-view-item-controller's representedObject (e.g. entity "ONE", the to-many-relationship is "twos") with core data?
**
*1) I tried to access the data of entity TWO via representedObject.twos.name -> not possible.
*2) I tried to access the data via a new NSArrayController (entity-type TWO) bound to managedObjectContext and contentSet bound to representedObject.twos of the collection-view-item-controller. Then I bound the NSTableView to the new SArrayController -> not possible.
*3) Some experiments with value transformers -> no success.
*4) KeyValueObserving -> no success.
*5) Any other ideas?


----

NSCollectionView is supposed to support observation of selection changes using KVO on the -selectionIndexes property. However, this doesn't work, at least in 10.5.5 You can get notified about the initial value, but not subsequent changes. Unfortunately the ommission of other NSNotifications means there's no simple workaround at present. I filed this in radar as Bug ID# 6290293. --GrahamCox.


----

 Anyone know how to determine the position (NSRect) of selected items?

----

 Anyone know how to catch key and mouse strokes not handled by default by NSCollectionView items short of subclassing and implementing keyDown. This method, while a workable hack, is brute force and not portable. It will almost certainly break some features. (keep in mind this question might not have an answer for the current version of NSCollectionView).

----

**
Drag-rearranging items in an NSCollectionView
**
I've written the code below because I had the need to drag-rearrange items in an NSCollectionView. It's far from perfect and probably needs some work, but it's a crude solution to this problem.
Go nuts at the code, but it comes without ANY guarantees.

**
RKCollectionView.h
**
    
//
//  RKCollectionView.h
//
//  Created by Robert on 10/20/08.
//  Copyright 2008 Robert K. (rob <*-*-*> halfduplex <()> net). All rights reserved.
//

#import <Cocoa/Cocoa.h>

extern NSString *			kCollectionViewDefaultDragType;

@interface RKCollectionView : NSCollectionView {
	NSArrayController *		contentArrayController;

	// Dragging helpers
	BOOL					dragInProgress;
	NSInteger				dragInProgressLocation;
	
	NSString *				dragTypeString;
}

@property (assign) NSArrayController *	contentArrayController;
@property (retain) NSString *			dragTypeString;

// Generals
- (void)awakeFromNib;
- (void)drawRect:(NSRect)aRect;

// Events
- (void)simulateSingleClickWithEvent:(NSEvent *)aEvent;
- (void)mouseDown:(NSEvent *)aEvent;
- (void)mouseDragged:(NSEvent *)aEvent;
- (void)mouseUp:(NSEvent *)aEvent;

// Helpers
- (id)selectedObject;
- (NSArray *)selectedObjects;

- (NSView *)selectedView;
- (NSArray *)selectedViews;

@end


**
RKCollectionView.m
**
    
//
//  RKCollectionView.m
//
//  Created by Robert on 10/20/08.
//  Copyright 2008 Robert K. (rob <*-*-*> halfduplex <()> net). All rights reserved.
//

#import "RKCollectionView.h"

#import "NSView+viewAtPointExcluding.h"
#import "NSArray+reversedArray.h"
#import "NSArrayController+moveObject.h"

NSString * kCollectionViewDefaultDragType		= @"CollectionViewDrag";

@interface RKCollectionView (private)
// Dragging
- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender;
- (void)draggingExited:(id <NSDraggingInfo>)sender;
- (NSDragOperation)draggingUpdated:(id <NSDraggingInfo>)sender;
- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender;

- (void)setDragTypeString:(NSString *)aString;

// Helpers
- (NSUInteger)indexOfPoint:(NSPoint)aPoint;
@end

//---------- Public
#pragma mark
#pragma mark Public
//----------
@implementation RKCollectionView

@synthesize contentArrayController;
@synthesize dragTypeString;

//---------- Generals
#pragma mark Generals
//----------
- (void)awakeFromNib {
	[self setDragTypeString:kCollectionViewDefaultDragType];
	[self registerForDraggedTypes:[NSArray arrayWithObject:dragTypeString]];
	
	dragInProgress = NO;
}

- (void)drawRect:(NSRect)aRect {
	[super drawRect:aRect];
	
	if( dragInProgress &&
		dragInProgressLocation >= 0 &&
		![contentArrayController filterPredicate] ) {
		NSUInteger	position = dragInProgressLocation;

		if( dragInProgressLocation == NSNotFound || dragInProgressLocation > self subviews] count]-1 ) {
			position = [[self subviews] count]-1;
		}

		[[NSView *	subview = [self subviews] reversedArray] objectAtIndex:position];
		[[NSRect		sFrame	= [subview frame];

		if( dragInProgressLocation == NSNotFound ) {
			sFrame.origin.x = sFrame.origin.x+sFrame.size.width;
		}
		
		float	anAccentRectSize	= 6;
		float	anAccentRectXOffset = 2;

		sFrame.origin.y += anAccentRectXOffset;

		NSRect	anAccentRect = NSMakeRect(sFrame.origin.x - anAccentRectSize*.5, sFrame.origin.y, anAccentRectSize, anAccentRectSize);
		
		NSPoint aStartPoint = NSMakePoint(sFrame.origin.x, sFrame.origin.y+anAccentRectSize);
		NSPoint anEndPoint = NSMakePoint(sFrame.origin.x, sFrame.origin.y+sFrame.size.height-anAccentRectXOffset);

		NSBezierPath *	aHighlightPath = [NSBezierPath bezierPath];
		[aHighlightPath appendBezierPathWithOvalInRect:anAccentRect];
		[aHighlightPath moveToPoint:aStartPoint];
		[aHighlightPath lineToPoint:anEndPoint];
		
		[[NSColor colorWithCalibratedRed:0.584314 green:0.901961 blue:0.000000 alpha:1.0] set];
		[aHighlightPath fill];
		[aHighlightPath setLineWidth:2.0];
		[[NSColor blackColor] set];
		[aHighlightPath stroke];
	}
}

//---------- Events
#pragma mark Events
//----------
- (void)simulateSingleClickWithEvent:(NSEvent *)aEvent {
	NSView * target = [self viewAtPoint:[self convertPoint:[aEvent locationInWindow] fromView:nil] excludingView:nil];
	
	NSIndexSet * indexSet = nil;
	if( target ) {
		indexSet = [NSIndexSet indexSetWithIndex:self subviews] count]-([[self subviews] indexOfObject:target]+1)];
	} else {
		[[self window] makeFirstResponder:self];
	}
	[self setSelectionIndexes:indexSet];
}

- (void)mouseDown:([[NSEvent *)aEvent {
	[self simulateSingleClickWithEvent:aEvent];
}

- (void)mouseDragged:(NSEvent *)aEvent {
	NSAssert(contentArrayController != nil, @"no contentArrayController was assigned to ImagesCollectionView!");

	id selectedObject = [self selectedObject];
	
	if( selectedObject && !dragInProgress ) {
		dragInProgress = YES;

		NSPasteboard *		pboard = [NSPasteboard pasteboardWithName:NSDragPboard];        
		[pboard declareTypes:[NSArray arrayWithObject:dragTypeString] owner:contentArrayController];
		[pboard setPropertyList:[NSNumber numberWithUnsignedInteger:((NSUInteger)selectedObject)] forType:dragTypeString];
		
		// lock focus on the image and draw into it
		NSImage * sourceImage = [[NSImage alloc] initWithData:[self dataWithPDFInsideRect:self selectedView] frame];
		NSPoint position = [self convertPoint:[aEvent locationInWindow] fromView:nil];
		
		NSImage * image = [[NSImage alloc] initWithSize:[sourceImage size]];
		[image lockFocus];
		[sourceImage compositeToPoint:NSMakePoint(0,0) operation:NSCompositeSourceOver fraction:0.75];
		[image unlockFocus];
		
		// activate the drag image
		[self  dragImage: image
					  at: position
				  offset: NSZeroSize
				   event: aEvent
			  pasteboard: pboard
				  source: self
			   slideBack: YES]; // use 'snap back' animation if drop doesn't complete
		
		[image release];
	}
}

- (void)mouseUp:(NSEvent *)aEvent {
	dragInProgress = NO;

    [self setNeedsDisplay:YES];
}

//---------- Helpers
#pragma mark Helpers
//----------
- (id)selectedObject {
	NSArray * selectedObjects = [self selectedObjects];
	
	if( [selectedObjects count] < 1 ) {
		return nil;
	}
	
	return [selectedObjects objectAtIndex:0];
}

- (NSArray *)selectedObjects {
	return self content] objectsAtIndexes:[self selectionIndexes;
}

- (NSView *)selectedView {
	if( self selectedViews] count] < 1 ) {
		return nil;
	}
	
	return [[self selectedViews] objectAtIndex:0];
}

- ([[NSArray *)selectedViews {
	return [self subviews] reversedArray] objectsAtIndexes:[self selectionIndexes;
}

@end

//---------- Private
#pragma mark
#pragma mark Private
//----------
@implementation RKCollectionView (private)
//---------- Dragging
#pragma mark Dragging
//----------
- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender {
	if( [sender draggingSource] == self &&
		[sender draggingPasteboard] types] containsObject:dragTypeString] &&
		![contentArrayController filterPredicate] ) {
		return [[NSDragOperationMove;
	}
	
	if( [sender draggingSource] != self && // COPY
	   [sender draggingPasteboard] types] containsObject:dragTypeString] && 
	   ![contentArrayController filterPredicate] ) {
		
		return [[NSDragOperationCopy;
	}

    return NSDragOperationNone;
}

- (void)draggingExited:(id <NSDraggingInfo>)sender {
	dragInProgressLocation = -1;
	[self setNeedsDisplay:YES];
}

- (NSDragOperation)draggingUpdated:(id <NSDraggingInfo>)sender {
	if( [sender draggingSource] == self && // MOVE
		[sender draggingPasteboard] types] containsObject:dragTypeString] &&
		![contentArrayController filterPredicate] ) {
		
		dragInProgressLocation = [self indexOfPoint:[sender draggedImageLocation;
		[self setNeedsDisplay:YES];
		
		return NSDragOperationMove;
	}
	
	if( [sender draggingSource] != self && // COPY
		[sender draggingPasteboard] types] containsObject:dragTypeString] && 
		![contentArrayController filterPredicate] ) {

		dragInProgressLocation = [self indexOfPoint:[sender draggedImageLocation;
		[self setNeedsDisplay:YES];
		
		return NSDragOperationCopy;
	}

	return NSDragOperationNone;
}

- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender {
	NSAssert(contentArrayController != nil, @"no contentArrayController was assigned to ImagesCollectionView!");
	
	if( [sender draggingSource] == self && // MOVE
	   [sender draggingPasteboard] types] containsObject:dragTypeString] &&
	   ![contentArrayController filterPredicate] ) {

		[[NSNumber * source = sender draggingPasteboard] propertyListForType:dragTypeString];
		dragInProgressLocation = [self indexOfPoint:[sender draggedImageLocation;
		
		id object = (NSObject *)[source unsignedIntegerValue];

		NSUInteger destination = dragInProgressLocation;
		if( destination == NSNotFound ) {
			destination = self subviews] count];
		}
		
		if( object ) {
			[contentArrayController moveObject:object toArrangedObjectIndex:destination];
		}
		return YES;
	}
	
	if( [sender draggingSource] != self && // COPY
	   [[[sender draggingPasteboard] types] containsObject:dragTypeString] &&
	   ![contentArrayController filterPredicate] ) {

		[[NSNumber * source = sender draggingPasteboard] propertyListForType:dragTypeString];
		dragInProgressLocation = [self indexOfPoint:[sender draggedImageLocation;
		
		id object = (NSObject *)[source unsignedIntegerValue];
		
		if( contentArrayController arrangedObjects] containsObject:object] ) {
			return NO;
		}
		
		[contentArrayController addObject:object];

		return YES;
	}
	
	return NO;
}

- (void)draggingEnded:(id <[[NSDraggingInfo>)sender {
	dragInProgress = NO;
	
    [self setNeedsDisplay:YES];
}

- (void)setDragTypeString:(NSString *)aString {
	[aString retain];
	[dragTypeString release];
	dragTypeString = aString;
	[self registerForDraggedTypes:[NSArray arrayWithObject:dragTypeString]];
}

//---------- Helpers
#pragma mark Helpers
//----------
- (NSUInteger)indexOfPoint:(NSPoint)aPoint {
	NSView *	target		= [self viewAtPoint:[self convertPoint:aPoint fromView:nil] excludingView:nil];
	NSUInteger	position	= 0;
	
	if( !target ) { // we're over empty space here
		position = NSNotFound;
	} else { // we're on somebody's turf
		position = [self subviews] reversedArray] indexOfObject:target];
	}
	
	return position;
}

@end

**
Helpers as used in above code
**
    
#import "[[NSView+viewAtPointExcluding.h"

@implementation NSView (viewAtPointExcludingView)

- (id)viewAtPoint:(NSPoint)pt excludingView:(id)eView {
	for( NSView * view in [self subviews] ) {
		if( view != eView && [self mouse:pt inRect:[view frame]] ) {
			return (view);
		}
	}
	
	return nil;
}


@end

    
#import "NSArray+reversedArray.h"

@implementation NSArray (reversedArray)

- (NSArray *)reversedArray {
	NSMutableArray *	reversedArray = [[NSMutableArray alloc] init];
	
	for( id object in self ) {
		[reversedArray insertObject:object atIndex:0];
	}
	
	return [reversedArray autorelease];
}

@end

    
#import "NSArrayController+moveObject.h"

@implementation NSArrayController (moveObject)

- (void)moveObject:(id)object toArrangedObjectIndex:(NSUInteger)index {
	[object retain];
	
	if( self arrangedObjects] indexOfObject:object] < index ) {
		index--;
	}
	
	[self removeObject:object];
	[self insertObject:object atArrangedObjectIndex:index];
	
	[object release];
}

@end


----
In my case, I just want to make a single column [[NSCollectionView that has elements of different height. Has anyone had any success in trying to make something similar?

----

Yes, but in my case I wrote my own custom class instead of using NSCollectionView. Using the animation context and     -animator proxy, it�s very easy to make a single-column NSCollectionView-alike that doesn�t throw up all over itself constantly. Here�s what I�m using in one of my apps:

    
// Header:
@interface TCollectionView : NSView {
	NSUInteger columns, rows;
	IBOutlet NSCollectionViewItem *itemPrototype;
	NSArray *content;
	NSArray *items;
	NSArray *backgroundColours;
}

@property NSUInteger columns, rows;
@property(retain) NSCollectionViewItem *itemPrototype;
@property(copy) NSArray *content, *items;

-(void)arrangeSubviews;

@end

// Implementation:
@implementation TCollectionView

@synthesize columns, rows, itemPrototype, content, items;

-(void)awakeFromNib {
	backgroundColours = [[NSArray arrayWithObjects:
		[NSColor whiteColor],
		[NSColor colorWithCalibratedHue: 0 saturation: 0 brightness: 0.94 alpha: 1.0],
	nil] retain];
}

-(NSCollectionViewItem *)newItemForRepresentedObject:(id)object {
	NSCollectionViewItem *item = [self.itemPrototype copy];
	item.representedObject = object;
	return item;
}

-(void)setupSubviews {
	NSMutableArray *is = [NSMutableArray array];
	[self setSubviews: [NSArray array]];
	for(id representedObject in self.content) {
		NSCollectionViewItem *item = self newItemForRepresentedObject: representedObject] autorelease];
		[is addObject: item];
		[self addSubview: item.view];
	}
	self.items = is;
}

-(void)arrangeSubviews {
	[[NSPoint offset = NSMakePoint(0.0, 0.0);
	[self setFrameSize: NSMakeSize(self.frame.size.width, [self enclosingScrollView].frame.size.height)];
	for(NSView *view in self.subviews) {
		[view setFrameOrigin: offset];
		[view setFrameSize: NSMakeSize(self.frame.size.width, view.frame.size.height)];
		offset = NSMakePoint(offset.x, offset.y + view.frame.size.height);
		self animator] setFrameSize: [[NSMakeSize(self.frame.size.width, view.frame.origin.y + view.frame.size.height)];
	}
	[self setNeedsDisplay: YES];
}

-(void)setContent:(NSArray *)c {
	if(content != c) {
		[self willChangeValueForKey: @"content"];
		[content release];
		content = [c retain];
		[self setupSubviews];
		[self didChangeValueForKey: @"content"];
	}
	[self arrangeSubviews];
}

-(BOOL)isFlipped {
	return YES;
}

-(void)drawRect:(NSRect)rect {
	[[NSColor whiteColor] set];
	NSRectFill([self frame]);
	NSUInteger i = 0;
	for(NSView *view in self.subviews) {
		backgroundColours objectAtIndex: i % [backgroundColours count set];
		NSRectFill([view frame]);
		i++;
	}
}

@end


Bind its itemPrototype to an NSCollectionViewItem, and bind its content to an NSArrayController (both of those tasks can be made easier with RXObjectBinder since there�s no IB3 plugin for this view), and you�re set. --RobRix

