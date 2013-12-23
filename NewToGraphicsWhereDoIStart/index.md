---
layout: page
title: NewToGraphicsWhereDoIStart
---



Good evening all.

I have done some simple programing using IB and Objective-C. I  would like to start making programs that use graphics. Specifically, I would like to try to make a slide rule program (those things they had before calculators!).

I am looking for a good starting point. I've tried reading up on all the Quartz/OpenGL/Cocoa Drawing stuff, but to be honest it is very confusing - a lot of references but not many examples. Where should I start? What route do you think is best for learning to do this?? The idea of the program is very simple: respond to 4 different user inputs and move one of two things around on the screen. But I just really cannot find a good starting point.

Any ideas would be greatly appreciated!

----

There is a good tutorial series here: http://cocoadevcentral.com/d/intro_to_quartz/

Getting started with graphics in Cocoa is easy, though a slide rule may be a little more ambitious. To get started, make a new XCode project, then go to New File... Pick "Objective-C NSView subclass" from the list, give it a suitable name. That makes a skeleton .h and .m file in your project. Drag the .h file to your MainMenu.nib file in IB. That adds the class that you just named to IB's classes. Lay out an interface including a plain NSView. In the inspector, change its custom class to the name of your class listed in the pop-up menu. That makes Cocoa instantiate your class when the window is displayed. Back in Xcode, in your .m file, find the -drawRect: method. Add some graphics code to it, such as:

    

- (void)    drawRect:(NSRect) rect
{
    [[NSColor whiteColor] set];
    NSRectFill( rect );

    NSRect  myRect = NSMakeRect( 20, 20, 200, 200 );

    [[NSColor redColor] set];
    NSRectFill( myRect );
}



Build and Go. If you are in luck, you'll see a red box in the middle of your window. Your code drew it. That's about as simple as graphics get in Cocoa! Once you have this you have a testbed for learning about Cocoa graphics programming. One of the most versatile and useful classes for graphics in Cocoa is NSBezierPath. Read the docs on that class and experiment with it in your new testbed.

To respond to input such as the user clicking and dragging with the mouse, you use NSView methods such as - mouseDown: so you need to read all about that class too.

Good luck, and have fun! --GrahamCox

----

Graham,

Thank you very much. I will follow up on your suggestions.

