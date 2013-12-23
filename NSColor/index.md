---
layout: page
title: NSColor
---

https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSColor_Class/Reference/Reference.html
----
An NSColor object represents a color, which is defined in a color space, each point of which has a set of components (such as red, green, and blue) that uniquely define a color.
----
**A Quick Introduction** (Not intended as a substitute for reading the docs linked to, above.) http://goo.gl/OeSCu

* NSColor objects are not mutable. There is, for example, no setRed:green:blue:alpha method. To get a different color, create a different color.
* There are a set of Class Methods that return predefined (or system defined) colors (like magentaColor and selectedMenuItemColor). Important to note is that these are class methods (not instance methods) and that they do not result in the creation of a color. You can argue that it's not necessary to retain/release these (they act like constants) but the general consensus is that this is a little on the unwise side. To be safe (and consistent), you should treat them like autoreleased objects, retaining/releasing them as usual. 
* Colors are created with Class Method names beginning with "colorWith" (e.g., colorWithCalibratedRed:green:blue:alpha:). There are "colorWith" methods for each supported color space. These methods create and return a new color object which has already been autoreleased. If you want to hold on to them you must retain/release them accordingly.
* There are also 2 Instance Methods beginning with "colorUsing" that appear to change the color space of an existing color. This is not quite true, though. These methods actually create and return a new color that is equivalent in appearance to the receiver's. As color spaces (in theory, multi-dimensional spaces each containing all colors possible within their particular color specification) are not equal, the returned color may not look exactly like the receiver's. In some cases a conversion between color spaces isn't possible for some particular color in which case these methods return nil. In many cases, a roundtrip conversion (from color space x to color space y and back to color space x) will not result in the same (original) color as information can be lost in the conversion.
* There is also an Instance Method named colorWithAlphaComponent: which may appear to be a way to change the alpha value for a color but, again, acutally creates and returns a new color, this time the same as the reciever's except with the new alpha value. (If the existing color is in a color space that doesn't have an "alpha" component, this method just returns the receiver back to itself.)

*Drawing With Colors*

* While it's normally spoken of as an Object Oriented approach, Cocoa is occasionally a little bit Magic Oriented (Mystery Oriented?). You would think that if you're drawing in an NSView (or a subclass of NSView) that the color would be set via an NSView method (or maybe a method of the line or shape you're drawing). Instead, you send a <code>set</code> message to an NSColor object which, typically, isn't created or referenced until right before drawing in the view.  It's common to find lines of code like <code>[[NSColor redColor] set];</code> in a method that draws in a view. Such lines automagically tell the view which color to use in subsequent drawing instructions.

So, if you're looking in the NSView documentation for something like setDrawingColor or setStrokeColor, stop. What you're looking for is actually in the docs for NSColor (look for the instance methods drawSwatchInRect:, set, setFill and setStroke) and/or you can refer to HowToDraw and CocoaDrawing.


*Dragging and Dropping Colors*

*So where would you expect to find the correct method for dragging a color from a view and dropping into a color well? 

Maybe an instance method of NSColor? *Nope.*

*(why would you expect to find it there? does NSString have dragging methods?)*

Well then, how about in NSView?  It has a dragImage instance method, so is that where they put it? *Good guess, but you're wrong again.*

*(you mean dragImage:at:offset:event:pasteboard:source:slideBack: ? that just sets an image to represent the view's pasteboard data during drags. it has nothing to do with what's acutally dragged or placed on the pasteboard.)*

Okay ..., maybe it's a NSColorWell method? *Sorry, but you're getting closer!*

*(that would limit color dragging to NSColorWells.)*

*(it's an NSColorPanel class method, exactly where it belongs.)*  *So, why would an NSColor class method be the wrong place then?*



----
Thought I'd share a little trick to get a hexadecimal representation of a color (i.e., for use in HTML). It's pretty simple code, but it's the shortest way I could figure out to do this. If there's any easier way to do this, please post it.

    
 @implementation NSColor(hexColor)
 
 - (NSString*)hexColor {
 	NSColor* col = [self colorUsingColorSpaceName:NSCalibratedRGBColorSpace];
 	return [NSString stringWithFormat:@"#%0.2X%0.2X%0.2X",
 				(int)([col redComponent] * 255),
 				(int)([col greenComponent] * 255),
 				(int)([col blueComponent] * 255)];
 }
 
 @end

--RobinHP

---- You probably want     roundf([col redComponent] * 255), etc. instead of     (int)([col redComponent] * 255), to be as accurate as possible.
----

You may prefer this solution: (anyway, the forced colorUsingColorSpaceName parameter should not be a part of your function)

    
 - (NSString *)hexaValueOfRedComponent
 {
 	return [NSString stringWithFormat:@"%02X", 
            (int32_t)([self redComponent] *  255.9999f)
        ];
 }


-mmw

----

**Watch out for long lines when you're using code tags. Code doesn't autowrap on CocoaDev.**

----

*You may prefer this solution: (anyway, the forced colorUsingColorSpaceName parameter should not be a part of your function)*

But this isn't a solution, it's solving a different problem. And why not convert to RGB in the function? Since HTML hex strings imply RGB, it makes perfect sense that a category on NSColor, which supports a variety of different colour spaces, should do this. If you are going to make assertions, back them up with explanations.

