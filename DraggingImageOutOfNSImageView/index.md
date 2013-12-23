---
layout: page
title: DraggingImageOutOfNSImageView
---

Is there any sample code to demonstrate being able to drag an image OUT of an NSImageView and drop it into other apps as an image?

----

You just have to put the image data on the pasteboard in mouseDragged:

http://developer.apple.com/documentation/Cocoa/Conceptual/DragandDrop/Concepts/dragsource.html

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSView.html#//apple_ref/doc/uid/20000014/BBCDEIEF

----

How do you detect the dragging?

*subclass NSImageView and override     mouseDragged:*

----

I tried that, but it's not catching the event - do I have to make it a responder or something? 

----

Putting

- (void)mouseDragged:(NSEvent *)event
{
	NSLog(@"hi");
}

Into the implementation of the NSImageView subclass doesn't work...any ideas?

----

Try also catching mouse downs. Make sure a mouse down event is properly handled before mouseDragged is called.

    
-(void)mouseDown:(NSEvent*)event {
    NSLog(@"Wassup");
    [super mouseDown:event];
}


----

It appears to be catching mouse downs properly - but still no go with the mouse drags.  Thanks.

----
Here's some (slightly modified) code from the *Basic Event Handling* docs...

    

- (void)mouseDown:(NSEvent *)theEvent
{
    BOOL keepOn = YES;

    while (keepOn) {
        theEvent = self window] nextEventMatchingMask: [[NSLeftMouseUpMask |
                NSLeftMouseDraggedMask];

        switch ([theEvent type]) {
            case NSLeftMouseDragged:
                     [self mouseDragged:theEvent];
                     keepOn = NO;
                    break;
            case NSLeftMouseUp:
                    keepOn = NO;
                    break;
            default:
                    /* Ignore any other kind of event. */
                    break;
        }
    }
    return;
}


----

Here's another (PyObjC; should be obvious how to translate to straight Objective-C, and ignore the 'self' arguments) version that I've implemented. It's still not perfect (I'm sure I'm not doing the 'at' and/or 'offset' arguments correctly, for example) but it works.

    

class Dragger(NSImageView):
    def mouseDown_(self,theEvent):
        return NSResponder.mouseDown_(self,theEvent)
    
    def mouseDragged_(self,theEvent):
        pb = NSPasteboard.pasteboardWithName_(NSDragPboard)
        pb.declareTypes_owner_(NSArray.arrayWithObject_(NSTIFFPboardType),self)
        pb.setData_forType_(self.image().TIFFRepresentation(),NSTIFFPboardType)
        
        NSView.dragImage_at_offset_event_pasteboard_source_slideBack_(self,self.image(),
            NSPoint(0.0,0.0),self.image().size(),theEvent,pb,self.window(),objc.YES)

        return NSResponder.mouseDragged_(self,theEvent)
    
    def draggingSourceOperationMaskForLocal_(self,isLocal):
        return NSDragOperationCopy



*Isn't that implementation of     mouseDown: basically redundant? After all, it just calls NSResponder's implementation. Maybe this is just a Python quirk... --JediKnil*

----

----

Half-newbie here: Can anyone suggest how to drag FROM NSImageViews?

----

Well, it really wasn't intended for that purpose, just to accept an image. It *IS* however a descendant of NSView. Just look up how to do drag and drop with a view. One thing you'll learn is that you need to provide a "drag image" for the drag op. Just use the NSImageView's -image to grab the current image.

----

I have successfully subclassed NSImageView to allow it to act as a drag source. Drop an email to jeff [at] bitprophet [dot] org if you'd like to see what I did. In the end it was very simple to implement, however finding out the correct thing to do was a bit of a pain (I'm also a newbie).

