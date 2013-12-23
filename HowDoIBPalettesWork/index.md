---
layout: page
title: HowDoIBPalettesWork
---



I need to make a static palette of InterfaceBuilder with XCode, but also the example "ProgressViewPalette" doesn't work! I tried, but the application says that my custom palette is unloadable.

----

cocoadevcentral.com used to have a tutorial on this topic, but they changed their frontpage so I'm not sure where it is right now. Google still has a cache of it. Just do a google search for "Interface Builder palette cocoadevcentral jason marr" and click on the link for the cached representation. --zootbobbalu http://goo.gl/OeSCu

----

**Interface Builder Palettes - Posted by Jason Marr on cocoadevcentral.com (May 28, 2002)**

// Introduction

Interface Builder palettes allow you to drag and drop an object into your project's nib file, edit an object's attributes through an Inspector, and put an object into "Test Mode." These properties make palettes a great way to distribute your own custom objects to other developers. So let's get started and see just how easy it is to palettize an object.

// DotView

In this tutorial we will make a palette of a custom view called DotView. This is a simple view which draws a dot centered where the user clicks. The DotView class can be found in the /Developer/Examples/AppKit directory on your computer. You may be familiar with DotView if you read Learning Cocoa because it was used as an example of a custom view in that book. By making a palette of DotView we will be able to simply drag it from the Interface Builder palette window and use it in any new application we create.

Figure 1: The final palette, Inspector, and nib file using the DotView class.

// Setup

Open up Project Builder and create a new project using the IBPalette template, which can be found under the Standard Apple Plug-ins heading. Save the project as Dot. Now we need to edit some of the files that Project Builder automatically created.

First, delete Dot.h and Dot.m. We will replace them with files for the DotView class. So, add the files DotView.h and DotView.m to your project. They can be found in the /Developer/Examples/AppKit directory, and I would recommend that you add copies of these files to your project rather than using the original files. If you use Project Builder's "Add Files..." menu command you will be given the option to make copies of the files in your Dot project directory.

Now make the following changes to DotPalette.h, DotPalette.m, and DotInspector.m.

Changes to DotPalette.h


*Change #import Dot.h to import DotView.h
*Change @interface Dot (DotPaletteInspector) to @interface DotView (DotPaletteInspector)


Changes to DotPalette.m


*Change @implementation Dot (DotPaletteInspector) to @implementation DotView (DotPaletteInspector)


Changes to DotInspector.m


*Change #import Dot.h to #import DotView.h

 
// NSCoding Methods

Now we need to add NSCoding methods to DotView so that it can be stored and read from the nib file. Basically, all these two methods do is encode and decode the three members of the DotView class. Add the following code to DotView.m and of course add the method declarations to DotView.h. If you want more information on NSCoder see this tutorial on archiving.

    
-(void)encodeWithCoder:(NSCoder *)coder
{
    float x=center.x;
    float y=center.y;
    [super encodeWithCoder:coder];
    [coder encodeObject:color];
    [coder encodeValueOfObjCType:@encode(float) at:&radius];
    [coder encodeValueOfObjCType:@encode(float) at:&x];
    [coder encodeValueOfObjCType:@encode(float) at:&y];
}

-(id)initWithCoder:(NSCoder *)coder
{
    if (self=[super initWithCoder:coder])
    {
        float x,y;
        color=coder decodeObject] retain];
        [coder decodeValueOfObjCType:@encode(float) at:&radius];
        [coder decodeValueOfObjCType:@encode(float) at:&x];
        [coder decodeValueOfObjCType:@encode(float) at:&y];
        center.x=x;
        center.y=y;
    }
    return self;
}

 
// Nib File Editing

Open [[DotPalette.nib in Interface Builder. Now drag DotView.h from PB into the DotPalette.nib window. This lets IB know about the DotView class. Next drag a custom view into the "Palette Window" and make it a DotView class. This can be done by selecting "Attributes" from the Inspector window and then selecting DotView. Now click on "File's Owner" in the DotPalette.nib window and make it an instance of DotPalette. Now add an outlet to the DotPalette class called view. Finally, control drag from "File's Owner" to the DotView and select the "view" outlet in the Connections window.

Save the nib file, and go back to Project Builder. Add IBOutlet DotView *view; to DotPalette.h so that we can access the custom view we just added in IB.

// New DotView Methods

We will add a few methods to DotView which will allow us to view and manipulate DotView's instance variables from the Palette and the Inspector. Add the following methods to DotView.m.

    
-(NSColor*)currentColor{
    return color;
}

- (void)setPaletteColor:(NSColor *)newColor{
    [color autorelease];
    color=[newColor retain];
    [self setNeedsDisplay: YES];
}

-(float)currentRadius{
    return radius;
}

-(void)setPaletteRadius:(float)newRadius{
    radius=newRadius;
    [self setNeedsDisplay:YES];
}

-(NSPoint)currentCenter{
    return center;
}

-(void)setPaletteCenterX:(int)x Y:(int)y{
    center.x=x;
    center.y=y;
    [self setNeedsDisplay: YES];
}


The "set" methods we just added to DotView.m will give us a way to set the attributes of the DotView class from the DotPalette and the DotInspector. Similarly, the "current" methods will allow us to access the current attributes of the DotView from the Palette and the Inspector.

// Initialize the Palette

A palette's finishInstantiate method gets get called immediately after it is loaded. We can override this method and use it to set the color, radius, and center for the DotView as it is shown in the palette window. We will use the methods we added to DotView.m to implement the finishInstantiate method. Add the following code to DotPalette.m's finishInstantiate method.

    
- (void)finishInstantiate
{
    [view setPaletteColor:[NSColor blueColor]];
    [view setPaletteRadius:25];
    [view setPaletteCenterX:57 Y:29];
    [super finishInstantiate];
}



At this point we can test our project. First build the project, and then go to Interface Builder and open its preferences. Click on the Palettes tab, and then add Dot.palette which is in your project's build directory. Now you can create a new nib file and test your new palette. Note: Each time you rebuild you project and want to test it in IB, you will have to quit and reopen IB for it to recognize the changes you have made.

// Inspector (Gadget)

Next we can add an Inspector to our palette so that anyone using it can set the default values for color, radius, and center. Our Inspector will use an NSColorWell, an NSSlider, and an NSForm to change those default values. So, add the following to DotInspector.h:

    
@interface DotInspector : IBInspector
{
    IBOutlet NSColorWell *colorWell;
    IBOutlet NSSlider *slider;
    IBOutlet NSForm *centerForm;

}
@end


Now, open DotInspector.nib and drag DotInspector.h to the nib window so the nib file will know about the outlets we just created. Next, make File's Owner an instance of DotInspector. *Note: I occasionally had problems with IB recognizing the outlets so you may need to add them manually.

The next step is to build the interface for the Inspector as shown. Now we need to make the target and outlet connections. Connect the NSColorWell, the NSSlider, and the NSForm to the corresponding outlets and set their targets to "File's Owner" using the ok: action. Then connect the Inspector window to "File's Owner's" window outlet.

Figure 2: User Interface for the Inspector.

Finally we just need to implement DotInspector's three methods: init, ok:, and revert:. The init method just reads the nib file, ok: updates DotView with the changes made by the Inspector, and revert: reads from DotView and updates the interface elements in Inspector.

The code for each of these methods is shown below.

    
- (id)init
{
    self = [super init];
    [NSBundle loadNibNamed:@"DotInspector" owner:self];
    return self;
}

- (void)ok:(id)sender
{
    int x;
    int y;
    DotView *selectedView;
    selectedView=[self object];
    [selectedView setColor:colorWell];
    [selectedView setRadius:slider];
    x=centerForm cellAtIndex:0] floatValue];
    y=[[centerForm cellAtIndex:1] floatValue];
    [selectedView setPaletteCenterX:x Y:y];
    [super ok:sender];
}

- (void)revert:(id)sender
{
    [[NSColor *color;
    NSPoint center;
    float radius;
    DotView *selectedView;
    
    selectedView=[self object];
    color=[selectedView currentColor];
    [colorWell setColor:color];

    radius=[selectedView currentRadius];
    [slider setFloatValue:radius];

    center=[selectedView currentCenter];
    centerForm cellAtIndex:0] setFloatValue:center.x];
    [[centerForm cellAtIndex:1] setFloatValue:center.y];
    
    [super revert:sender];
}


We now have a working Inspector, so it's time to test out the finished palette.

// Try It Out

Build your project and then create a new nib file in IB. Drag your [[DotView from the palette to the window. Use the Inspector to set its various attributes. Now add a colorwell and a slider to the window and connect them to the appropriate outlets of DotView. Choose "Test Interface" from IB's File menu and you have a fully functioning DotView app, from within IB! This means anyone you give the palette file to can create a working DotView app with ZERO lines of code!

I would like to credit Aaron Hillegass and his book Cocoa Programming for Mac OS X for inspiration and reference in writing this tutorial. If you have any questions about the tutorial feel free to feel free to contact me. Also, the PB project for this tutorial is available here.

----

I'm trying to write an application that needs a GUI editor component. I've created a palette containing UI elements, and a window they can be dragged to, but the problem is that the components must implement - mouseDragged:, make a drag image of itself and set the proper PBoard type. 

That behavior isn't needed or wanted in the actual component, so I've created a wrapper to handle the dragging. It works, but I'm left with wrapper objects in the view hierarchy I create, that I will have to remove. Also I can't create a palette in IB like with regular IB palettes.

It seems that IB somehow overrides normal event handling for UI components. All mouse clicks and keyboard events are processed by IB, which draws the component to an image, initiates the dragging and then instantiates a new object when it's dropped. I need to figure out how to do this, but so far I have almost no experience with Cocoa's event handling.

Also, IB handles composite components on a palette very well. I wonder how it knows that a group moves together? Does IB use the panel a palette is assembled and only work with top level objects?

-JF

----

The key is to override - hitTest in an NSView subclass. If the point is in the view return the view, otherwise return nil (this is necessary because hitTest is called even when the mouse is clicked outside the view.

Then in mouseDown in the view iterate through the subviews and use mouse:InRect: to see what view has been clicked. Then draw the view to an image using � dataWithPDFInsideRect:, and iniate a drag with dragImage.

To copy the component into the target I archive it into an NSData object with an NSArchiver, and place the NSData into the drag Pasteboard. In the target I grad the dragged image location and use that as the new frame origin.

So far it works very nicely. Seamless and professional looking. I've also got a switch on the target view that lets you go between moving the components and passing events through to them. I'll post some code later.

-JF

----

If you're interested, here's some code of the target drag view that lets you move around its subviews. There's some limitations in this method that I need to find work-arounds for, so this might not be a really good way to do things.

    
#import "DragView.h"

@implementation DragView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		// Add initialization code here
	}
	return self;
}

- (void)awakeFromNib {
	[self registerForDraggedTypes:[NSArray arrayWithObjects:@"MyPboardType",nil]];
	bgcolor = [NSColor whiteColor];
	dragEnabled = TRUE;
}

- (IBAction) enableDrag:(NSButton *) sender;
{
	if ([sender state] == NSOnState) {
		dragEnabled = TRUE;
	}
	else {
		dragEnabled = FALSE;
	}
}

- (void)mouseDown:(NSEvent *) theEvent
{
	if (dragEnabled) {
		draggedView = nil;
		NSArray *objs = [self subviews];
		int i;
		for (i = 0; i < [objs count]; i++) {
			NSView *obj = [objs objectAtIndex:i];
			NSRect frame = [obj frame];
			NSPoint point = [self convertPoint:[theEvent locationInWindow] fromView:nil];
			if ([self mouse:point inRect:frame]) {
				dragging = TRUE;
				draggedView = obj;
				[draggedView setBordered:TRUE];
				[draggedView setNeedsDisplay:TRUE];
				break;
			}
		}		
	}
}

- (void)mouseUp:(NSEvent *) theEvent
{
	dragging = FALSE;
	[self setNeedsDisplay: TRUE];
}

- (void)mouseDragged:(NSEvent *) theEvent
{
	if (dragEnabled & dragging & (draggedView != nil)) {
		NSRect rect = NSOffsetRect([draggedView frame], [theEvent deltaX], [self isFlipped] ? [theEvent deltaY] : -[theEvent deltaY]);
		[draggedView setFrame: rect];
		[self setNeedsDisplay:TRUE];
	}
}

- (NSView *)hitTest: (NSPoint) aPoint
{
	if (dragEnabled) {
		if ([self mouse: [self convertPoint:aPoint fromView:nil] inRect: [self bounds]]) {
			return self;
		}
		else {
			return nil;
		}
	}
	else {
		return [super hitTest: aPoint];
	}
}

- (NSDragOperation) draggingEntered:(id <NSDraggingInfo>)sender
{
	bgcolor = [NSColor grayColor];
	[self setNeedsDisplay: TRUE];
	return NSDragOperationCopy;
}

- (void) draggingExited:(id <NSDraggingInfo>)sender
{
	bgcolor = [NSColor whiteColor];
	[self setNeedsDisplay: TRUE];
}

- (void) draggingEnded:(id <NSDraggingInfo>)sender
{
	bgcolor = [NSColor whiteColor];
	[self setNeedsDisplay: TRUE];
}

- (BOOL)prepareForDragOperation:(id <NSDraggingInfo>)sender
{
	return YES;
}

-  (BOOL)performDragOperation:(id <NSDraggingInfo>)sender
{
	NSPasteboard *pb = [NSPasteboard pasteboardWithName:@"NSDragPboard"];
	NSData *data = [pb dataForType:@"MyPboardType"];
	NSView *obj = (NSView*)[NSUnarchiver unarchiveObjectWithData:data];
	NSPoint dropPoint = [self convertPoint:[sender draggedImageLocation] fromView:nil];
	[obj setFrameOrigin: dropPoint];
	[self addSubview:obj positioned:NSWindowAbove relativeTo:nil];
	bgcolor = [NSColor whiteColor];
	[self setNeedsDisplay: TRUE];
	return YES;
}

- (void)drawRect:(NSRect)rect
{
	[bgcolor set];
	NSRectFill([self bounds]);
	if (draggedView != nil) {
		[[NSColor blueColor] set];
		NSRect frame = [draggedView frame];
		NSFrameRect(frame);
		NSPoint p = frame.origin;
		[self drawHandle:p];
		p.x += (frame.size.width - 1) / 2;
		[self drawHandle:p];
		p.x += (frame.size.width - 1) / 2;
		[self drawHandle:p];
		p.y += (frame.size.height - 1) / 2;
		[self drawHandle:p];
		p.y += (frame.size.height - 1) / 2;
		[self drawHandle:p];
		p.x -= frame.size.width / 2;
		[self drawHandle:p];
		p.x -= frame.size.width / 2 - 1;
		[self drawHandle:p];
		p.y -= frame.size.height / 2;
		[self drawHandle:p];
	}
}

- (void)drawHandle:(NSPoint)center
{
	NSRect rect = NSMakeRect(center.x - 2, center.y - 2, 5, 5);
	NSRectFill(rect);
}

- (unsigned int)draggingSourceOperationMaskForLocal:(BOOL)isLocal {
	return NSDragOperationCopy;
}

@end



The problems that I'm having (or see for the future) involve how the selection rectangle is drawn. First they are much bigger than they are in IB. The code 

    
NSRect frame = [draggedView frame];
NSFrameRect(frame);


leaves a margin around most controls (like NSButton) where in IB the rectangle is snug up to the edge of the button. I'm trying to figure out how IB is determining what rect to draw. Possible it's combining the frames of all the NSCells in a View and drawing that.

The other problem is that the selection rectangle is drawn behind all controls. I can think of two ways to solve this but I don't know which is better. One way is to overlay another View (or Cell?)  just to draw highlights. It wouldn't do any event processing. The other way is to draw all the subviews to an image and draw that image onto my DragView and then the highlighting on top. I think this way may ruin some of the built-in drawing handling done by NSView.

