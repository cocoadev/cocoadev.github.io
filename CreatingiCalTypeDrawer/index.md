---
layout: page
title: CreatingiCalTypeDrawer
---



Does anybody know how the iCal drawer is done?  Is it just a white background image, and thats it?

I'm trying to make a drawer that looks the same, with some text fields with no border, and when you select it, the text disappears.  and other objects that look similar.  i know how to do all of this, but I guess I'm just confused if the background is more detailed.

----

My guess is it's a custom view with a white background. The bevel effect can be found somewhere on this site. Just look around.

----

Check out NSDrawWhiteBezel, or NSDrawTiledRects. These make drawing rects with bezels easy.

Also, I've done more or less what you're asking ( though mine eschewed the bezel for a rounded white rectangle. Its easy, just put in a custom view with an overridden display method.

*I think you mean     -drawRect: method. Override drawRect like so:*

    
- (void)drawRect:(NSRect)aRect
{
    [[NSColor whiteColor] set];
    NSRectFill(aRect);
}


*This will fill the entire view with white.*

