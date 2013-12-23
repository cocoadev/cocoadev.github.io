---
layout: page
title: HowToSaveDroppedDataOnACustomView
---

I want to create a drag-and-drop interface.  The interface is an icon that tells the user what to drop there; for example, a text icon tells the user to drop text there.  Relevant snippets of code are:

    
// Places an image over a custom view

- (void)configureTextImage
{
	NSString * file = @"~/Documents/Cocoa Programs/DragandDropView/Images/textImage.jpg";
	NSImage * image = [[NSImage alloc] initWithContentsOfFile:file];	
	[self setTextImage:image];  // setTextImage places image in custom view
	[image release];	
}

// Excerpts from the drag operation...

- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender
{
	NSPasteboard *paste = [sender draggingPasteboard];
	if (![self readStringFromPasteboard:paste]) {
		return NO;
	}
	return YES;

- (BOOL)readStringFromPasteboard:(NSPasteboard *)paste
{
	NSString * value = [paste stringForType:NSRTFDPboardType];
	
	if (value)
	{
		return YES;
	} else {
		return NO;
	}
}


This code works fine, but I don't know how to proceed with saving the dropped data into a file.  I'm using a document-based application for this program, and I would like some design and code advice to keep the view code (above) away from the controller and model code.  Thanks in advance -- DR.

----

There used to be problems with custom views having IBOutlets (that may or may not have been resolved) so I'm not sure if that's an option.  I'll assume it still doesn't work.  In your document class (or designated delegate), you could connect an IBOutlet to the view and then set the delegate through accessor methods in your delegate's `awakefromNib`.  I've also seen cases where an NSNotification is thrown because the programmer didn't want to deal with outlets/delegates and thought they might want to catch the data in multiple places. -G

----
"There used to be problems"? What sort of problems, exactly? I have never even *heard* of any problems with outlets in custom views which did not firmly lie within the realm of programmer error. Your statement is highly suspect at best.

To DR, please read over TextFormattingRules and don't leave your pages in an unreadable state. -- MikeAsh

----

October 2, 2007 - I faintly remember an article from when I started using Cocoa that said not to put IBOutlets in an NSView.  It's probable that the author was making some model-view-controller statement that I didn't catch at the time that pertained to the model at hand and not in general.  Apologies.  I obviously haven't listened to this advice since I've posted my solution further down that uses IBOutlets. :P -G

----
Guys, thanks for the advice.  I remain stumped.  Is there any source where I could see code similar to what you're suggesting?  I tried connecting my custom view to File's Owner (in a document-based application) and I can't get the dropped text out of the custom view class; it's there -- I can NSLog it -- just can't get it out of that class.  I'll reread my manuals, but any code help is appreciated.  DR.

----
While it doesn't sound like it would be too hard to save data to a file (pasteboard dataForType:theType] writeToFile:@"/path/to/file" atomically:YES]), keeping it properly MVC is a bit harder. Perhaps you should take the approach of table and outline views and introduce a delegate controller that handles all the dragging, and for that matter maybe even the view's content. -[[JediKnil

----
Drag and drop is really just a means of inputting data into a view, so think about how existing classes behave and then follow that example. In a nutshell, your view should either decide whether it wants the dragged data or else let someone else, like a delegate, make that decision. Then accept the drag (or not), store the data locally, and send out a notification indicating that your contents have changed. Or, if you subclass NSControl instead of NSView, you might invoke an action on your target. In any case, leave the question of what to do with the dragged data to someone else, such as your controller. Your view class doesn't need to know where the data will end up, and letting another object take care of storing the data opens up two nice possibilities: 1) reusing your view in some other situation will be easier; 2) you won't have to touch the view's code if you decide to do something different with the data. -CS.

----
Thanks JediKnil and CS.  That's where I'm heading, however, the examples that I used to learn key-value coding/controller classes used actual physical tables with easily 'bindable' keys.  How does one do the same with an abstract class like a custom view?  If you've seen code that does something similar to what I'm chasing, send a link.  I can create delegates that receive messages like 'willPerformDrag' and return 'YES" or 'NO.'  What remains unclear to me is how to create delegates that receive the output from, say,
[sender draggingPasteboard].  How do I transfer actual data between objects?  DR.
----
What on earth are you talking about ?
What do you mean "how do I transfer actual data between objects?"
Um, you send a message and pass the "data" as an argument...

You seem to be struggling with basic concepts like messages and the Model View Controller architecture.  Perhaps you would enjoy working through any of the many introductory Cocoa tutorials, or maybe you should start with C programming tutorials ?

----

October 2, 2007 - Here's a reusable subclass that I drop into projects when I need simple drag-and-drop with a delegate model:

    
// DropView.h
@interface DropView : NSView
{
	IBOutlet id delegate;
}
@end

@interface NSObject (DropViewDelegate)
-(void)view:(NSView *)view receivedDroppedFiles:(NSArray *)files;
@end


// DropView.m
#import "DropView.h"

@implementation DropView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		[self registerForDraggedTypes:[NSArray arrayWithObjects:NSFilenamesPboardType, nil]];
	}
	return self;
}

- (void)drawRect:(NSRect)rect
{
	// Do drawing stuff here.
}

- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender { return NSDragOperationCopy; }

- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender
{
	if (delegate && [delegate respondsToSelector:@selector(view:receivedDroppedFiles:)])
		[delegate view:self receivedDroppedFiles:sender draggingPasteboard] propertyListForType:[[NSFilenamesPboardType]];
	return YES;
}

@end


Note that I'm only checking for files in the above subclass.  You may need to change this.  Once you've hooked up the delegate using IB, you will receive drop messages in your delegate's `view:receivedDroppedFiles:` method.  This is quick and dirty so you may need to make it more robust. -G

----
Thanks, G.  That worked perfectly.  DR.

