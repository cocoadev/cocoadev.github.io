---
layout: page
title: NSViewsAndMouseClicks
---



I have an instance NSView subclass that has many various subviews within it. I'd like that instance to receive all mouse left-click events first, so I tried the following code:

    - (BOOL)acceptsFirstMouse:(NSEvent *)theEvent {
   return YES;
}

- (void)mouseDown:(NSEvent *)theEvent {
   NSPoint mouseLoc = [self convertPoint:[theEvent locationInWindow] fromView:nil];
   if([theEvent type] == NSLeftMouseUp) {
      NSLog(@"Click message");
   } else {
      NSLog(@"Other message");
   }
}

However, I only get an "Other message" when I click in a NSTextField subview. Am I missing a step here?

-- RyanGovostes

----

*I do believe there is a special event for right mouse down.*

I was looking for the left mouse button, though.

----

If your subviews implement mouseDown:, they will intercept the event and you will never see it.

I'm not sure of what a good way to do this would be. Perhaps override -hitTest: to return self, and then implement all mouse events to do your interception, then use     [super hitTest:] and pass the event on.

----

 In your subviews, implement self superview] mouseDown:event];

----

Also, the event passed into     mouseDown: will not have a type of     [[NSLeftMouseUp. It will probably have a type of     NSLeftMouseDown.

----

Oops, thanks for the catch. I would implement the     self superview] mouseDown:event]; for each subview, but I'd have to do a lot of subclassing of those, too. What about putting a see-through [[NSView subclass instance on the top and ordering everything below it, and then using this topmost view for click stuff?

