---
layout: page
title: SplashWindow
---

Is this the best way to create a splash window?

*
**SplashWindow.h**
    
#import <Cocoa/Cocoa.h>

@interface SplashWindow : NSWindow
{

}

@end

*
**SplashWindow.m**
    

#import "SplashWindow.h"
#import <AppKit/AppKit.h>

@implementation SplashWindow

- (id)initWithContentRect:(NSRect)contentRect
                styleMask:(unsigned int)aStyle
                  backing:(NSBackingStoreType)bufferingType
                    defer:(BOOL)flag
{
	self = [super initWithContentRect:contentRect
                                styleMask:NSBorderlessWindowMask
                                  backing:NSBackingStoreBuffered
                                    defer:NO];
    [self setBackgroundColor: [NSColor clearColor]];
    [self setLevel: NSStatusWindowLevel];
    [self setAlphaValue:1.0];
    [self setOpaque:NO];
    [self setHasShadow: YES];
    return self;
}

@end



----

I don't know if I would use NSStatusWindowLevel, that makes a floating window that stays above all other windows.  What if a user wanted to switch to another application while your program was launching?

----

NSStatusWindowLevel wouldn't be objectionable if the window was set to hide on deactivate. Although I find splash windows in general objectionable unless they show some sort of useful information about the app's startup progress.

----

I'd use something more like this...

    
@implementation SplashWindow

- (id)initWithImage:(NSImage *)splashImage
{
     NSRect contentRect = NSMakeRect(0,0, [splashImage size].width, [splashImage size].height);

	self = [super initWithContentRect:contentRect
                                styleMask:NSBorderlessWindowMask
                                  backing:NSBackingStoreBuffered
                                    defer:NO];

         { // create the contentView and set the image...
           NSImageView *contentView = [[NSImageView alloc] initWithFrame:contentRect];
                [contentView setImageFrameStyle:NSImageFrameNone];
                [contentView setImage:splashImage];
                [self setContentView:contentView];
                [contentView release];
         }

    [self setBackgroundColor: [NSColor clearColor]];
    [self setLevel: NSStatusWindowLevel];
    [self setAlphaValue:1.0];
    [self setOpaque:NO];
    [self setHasShadow: YES];
    [self center]; // might as well

    return self;
}

@end


Of course, it could borrow from the lower ToolTip example to return an autoreleased SplashWindow that displays for a set amount of time. Then using it would be real simple... [SplashWindow splashWithImage:yourImage display:YES];

