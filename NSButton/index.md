---
layout: page
title: NSButton
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSButton_Class/index.html

An AppKit class, controlling an NSButtonCell, and acting as the buttons you see just about anywhere you see them.

----
Here are some different implementations of NSButton, all taken from Apple's "Buttons" conceptual topic. Note that the last two, while possibly three buttons on top of each other, are each more likely an NSMatrix of three instances of NSButtonCell.

http://developer.apple.com/documentation/Cocoa/Conceptual/Button/Concepts/art/buttonpush.gif

http://developer.apple.com/documentation/Cocoa/Conceptual/Button/Concepts/art/buttonicon.gif

http://developer.apple.com/documentation/Cocoa/Conceptual/Button/Concepts/art/buttonsradioon.gif

http://developer.apple.com/documentation/Cocoa/Conceptual/Button/Concepts/art/buttonswitchmixed.gif

----

**Creating an instance programmatically**

I made a subclass of NSButton and added some custom functions to it. Now I want to display one or more instances of this class on screen with specific attributes, like width, height, highlight state, continuousness, etc...

I want to display the control programmatically, because at run time, there will be an undetermined number of buttons (my custom class) that will be created. I want them to be allocated, initialized, and displayed at certain coordinates, with certain size, etc...

----

The following code is an example of how to create a button programatically.  Just replace My_Button with the name of your subclass and remember to #import the header.   -- Bo

    
- (MyButton*)createButtonWithSuperView:(NSView*)inSuperView frame:(NSRect)inFrame state:(int)inState continuous:(BOOL)inCont
{
	MyButton* button = [[MyButton alloc] initWithFrame:inFrame];
	[button setContinuous:inCont];
	[button setState:inState];
	// insert any further setup you want below this comment
	
	[inSuperView addSubview:button];  
	[button release];  // the superview retains the button
	return button;
}


The frame parameter can be created using NSMakeRect( x, y, width, height) and specifies both the size and location of your button.

----
**Create a Matrix of Buttons Programmatically**

In response to a previous request on Apple's Cocoa-dev list for a sample of how to create a matrix of radio buttons programmatically, I just wrote the following little sample with commented source code in an Xcode project.  There are countless little samples out there already. Here is one more with my spin on the techniques.

Thanks to Scott Anguish and Stepwise for hosting this sample: http://graphics.stepwise.com/EMB/RadioSample.zip

This sample creates four radio button matrices using four different techniques:

* 1) Just do it in Interface Builder with no code
* 2) Copy and reposition the matrix previously configured in IB
* 3) Configure a custom view previously positioned in IB
* 4) Build up the matrix from scratch
 
**Note: There is almost never a reason to use techniques 3 and 4**

Note: Technique 2 is used by IB whenever entering "Run" mode and a similar technique is used when any nib is loaded in any application.

Note: There is virtually nothing that IB can do that you can't do in code because every user interaction within IB ends up calling code similar to the code in techniques 3 and 4. IB executes the code to configure a "live" object.  When you save a nib file, the live objects with all of their configuration (state) are archived.  When a nib is loaded, the objects are unarchived and come alive again. http://www.stepwise.com/Articles/Technical/FreezeDriedObjects.html IB exists so that you don't have to write or maintain all the code in techniques 3 and 4.  At least use technique 2 in favor of techniques 3 and 4.

The approaches used for a matrix of radio buttons in the example can be used with any Objective-C objects based on NSObject and supporting the NSCoding protocol.  This includes all Cocoa controls and can include any objects you create.
----
Here is the relevant code from the example in case you don't want to download the whole xcode project.
    
// Technique 2
- (IBAction)copyRadio:(id)sender
{ // Create a "deep" copy of the existing matrix to which the sampleIBMatrix outlet 
  // is connected.
  NSMatrix *copy = [NSUnarchiver unarchiveObjectWithData:
    [NSArchiver archivedDataWithRootObject:sampleIBMatrix]];
    
  // Get the frame rectangle of the object we just copied so we can
  // calculate a good place to position the copy
  NSRect   orignalFrame = [sampleIBMatrix frame];
  
  // Position the copy to the right of the original
  [copy setFrameOrigin:NSMakePoint(NSMaxX(orignalFrame), NSMinY(orignalFrame))]; 
  
  // Add the copy to the same view that contains the original
  sampleIBMatrix superview] addSubview:copy];
  
  // tell view to redraw including subviews
  [[sampleIBMatrix superview] setNeedsDisplay:YES];    
}

    
// Technique 3
- ([[IBAction)configureCustomView:(id)sender
{ // The sampleCustomView outlet is already connected to an instance of NSMatrix
  // instantaited and positioned in IB as a custom view.
  
  // Create a prototype cell
  NSButtonCell   *prototypeButtonCell = [[NSButtonCell alloc] init];
  
  // we just allocated a button cell so we must release or autorelease it
  [prototypeButtonCell autorelease];
  
  // configure the button cell the way we want
  [prototypeButtonCell setButtonType:NSRadioButton];
  
  // Tell matrix what kind of cell to use
  [sampleCustomView setPrototype:prototypeButtonCell];
  
  // Set other attributes of matrix
  [sampleCustomView setAllowsEmptySelection:NO];
  [sampleCustomView setIntercellSpacing:NSMakeSize(4.0f, 2.0f)];
  [sampleCustomView setCellSize:NSMakeSize(122.0f, 18.0f)];
  [sampleCustomView setMode:NSRadioModeMatrix];
  
  // Tell the matrix how many rows and columns it has
  [sampleCustomView renewRows:5 columns:1];
  [sampleCustomView sizeToCells];
  sampleCustomView superview] setNeedsDisplay:YES];
  
  // Set cell titles
  [[sampleCustomView cellAtRow:0 column:0] setTitle:@"One"];
  [[sampleCustomView cellAtRow:1 column:0] setTitle:@"Two"];
  [[sampleCustomView cellAtRow:2 column:0] setTitle:@"Three"];
  [[sampleCustomView cellAtRow:3 column:0] setTitle:@"Four"];
  [[sampleCustomView cellAtRow:4 column:0] setTitle:@"Five"];
}

    
// Technique 4
- ([[IBAction)makeRadioFromScratch:(id)sender
{ // This is just like -configureCustomView: except we have to create
  // and position an NSMatrix instead of using information set in IB

  // Create a prototype cell
  NSButtonCell   *prototypeButtonCell = [[NSButtonCell alloc] init];
  
  // we just allocated a button cell so we must release or autorelease it
  [prototypeButtonCell autorelease];
  
  // configure the button cell the way we want
  [prototypeButtonCell setButtonType:NSRadioButton];
  
  // create matrix with prototype cell, position using nasty hard coded constants, 
  // mode, etc.  These attributes could have been set individually as well
  NSMatrix       *newMatrix = [[NSMatrix alloc] 
    initWithFrame:NSMakeRect(138.0f, 85.0f, 0.0f, 0.0f)
    mode:NSRadioModeMatrix
    prototype:prototypeButtonCell 
    numberOfRows:5 
    numberOfColumns:1];
    
  // we just allocated a matrix so we must release or autorelease it
  [newMatrix autorelease];

  // Set other attributes of matrix
  [newMatrix setAllowsEmptySelection:NO];
  [newMatrix setIntercellSpacing:NSMakeSize(4.0f, 2.0f)];
  [newMatrix setCellSize:NSMakeSize(122.0f, 18.0f)];
  
  // Tell the matrix how many rows and columns it has
  [newMatrix renewRows:5 columns:1];
  [newMatrix sizeToCells];
  
  // tell new matrix how to resize.  Other techniques preserved
  // this setting from IB
  [newMatrix setAutoresizingMask:(NSViewMaxXMargin|NSViewMinYMargin)];
  
  // Set cell titles
  [[newMatrix cellAtRow:0 column:0] setTitle:@"One"];
  [[newMatrix cellAtRow:1 column:0] setTitle:@"Two"];
  [[newMatrix cellAtRow:2 column:0] setTitle:@"Three"];
  [[newMatrix cellAtRow:3 column:0] setTitle:@"Four"];
  [[newMatrix cellAtRow:4 column:0] setTitle:@"Five"];

  // Add the copy to the same view that contains the original
  [[sampleIBMatrix superview] addSubview:newMatrix];
  
  // tell view to redraw including subviews
  [[sampleIBMatrix superview] setNeedsDisplay:YES];    
}

