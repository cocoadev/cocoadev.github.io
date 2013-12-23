---
layout: page
title: SetMovableByWindowBackground
---

See also BorderlessWindow

I've got a strange problem with a NSWindow subclass.  I'm creating an odd-shaped window with a NSButton in it.  You can click and drag successfully almost anywhere in the window.  But, whenever I click and drag close to the edge of a button in the window, the window jumps.

Here's the relevant portion of my code.  Based on the transparent window code from the R**oundedFloatingPanel example on http://www.scotlandsoftware.com/products/source/

----

    

//------------------------------------------------------------------------------------
- (void)mouseDown:(NSEvent *)theEvent
{    
    NSRect  windowFrame = [self frame];

   initialLocation = [NSEvent mouseLocation];
   
   initialLocation.x -= windowFrame.origin.x;
   initialLocation.y -= windowFrame.origin.y;
}

//------------------------------------------------------------------------------------
- (void)mouseDragged:(NSEvent *)theEvent
{
   NSPoint currentLocation;
   NSPoint newOrigin;

   NSRect  screenFrame = [[NSScreen mainScreen] frame];
   NSRect  windowFrame = [self frame];

    currentLocation = [NSEvent mouseLocation];
    newOrigin.x = currentLocation.x - initialLocation.x;
    newOrigin.y = currentLocation.y - initialLocation.y;
    
    // Don't let window get dragged up under the menu bar
    if( (newOrigin.y+windowFrame.size.height) > (screenFrame.origin.y+screenFrame.size.height) ){
		newOrigin.y=screenFrame.origin.y + (screenFrame.size.height-windowFrame.size.height);
    }
    
    //go ahead and move the window to the new location
    [self setFrameOrigin:newOrigin];
}



----

The solution is remove the code above, and use this instead:

[self setMovableByWindowBackground:YES];

----

see also related discussion in NSTabViewWindowDraggingProblem

