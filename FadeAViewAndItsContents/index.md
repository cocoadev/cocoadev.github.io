---
layout: page
title: FadeAViewAndItsContents
---

I would like to fade (with alpha) a few views in and out based on mouse-over actions.  Which part of the View's drawRect function do I need to set to do this.
I also want the content of the views (some NSTable views) to be affected as well.  Is there an easy way to do this?

EcumeDesJours

----

Have you tried adding a subview filled with white (or whatever) as an overlay, and changing the alpha of its background color in a timer?

----

I'd subclass the views that you want to be able to fade and do something like this..
    
- (void)drawRect:(NSRect)rect
{
    [super drawRect:rect];
    [[[NSColor whiteColor] colorWithAlphaComponent:yourCurrentAlphaValue] set];
    NSRectFillUsingOperation(rect, NSCompositeDestinationOut);
}


----
This code sample assumes that you're fading to a particular color (e.g.., white) which, presumably matches the background (which, presumably, doesn't have any images or text on it). I think the originator of this discussion wants a way to fade their view away, progressively allowing what's underneath to show through, in the same manner that help "hints" (aka tooltips ;-) fade away. Sending the drawRect message to super won't work. You'd have to override super's drawRect method (including in any subviews), getting it to use more and more alpha in its colors. Unfortunately, how Cocoa implements the use of colors leaves a lot to be desired in this regard. Colors aren't mutable so if your view and subviews have a lot of them you're going to be creating new NSColor objects for *every* color *each* time you increase the alpha. Maybe it would be better to directly manipulate the offscreen (retained) view's pixels?

*Unless your view is only a few pixels square and contains tons of colors, the overhead associated with copying a bunch of alpha-blended pixel data to the screen is going to far outweigh the overhead associated with creating a bunch of very small and lightweight NSColor objects. Creating objects is "expensive" only when compared to operations like addition, multiplication, method calls, etc.*

Tons of colors in a view that's only a few pixels square? Hmmm. Isn't that sort of like getting 2 cups of jello into a one cup container? ;)  Anyways... , you're right about object creation being relatively cheap. I still don't like how they've implemented colors, though. And why you send a set message to a throwaway color object in order to get a view object to change its drawing color is beyond me. What kind of OO design pattern does *that* follow?

Back to the actual topic of this discussion: is there any (cheap/fast) way to make an NSImage out of the offscreen view? Or get the view copied to an NSImage? If so, and seeing as NSImage has a method for fading one image into another, couldn't you replace the view with an image when you're ready to fade it out and then fade the image? Unless you're still wanting the user to interact with it as it's fading, this shouldn't be that hard and it lets the low-level core code handle the fade. Not sure if this will work, but it's worth exploring.

*I don't know how fast it is, but you can use -dataWithPDFInsideRect: to get PDF data representing your view's contents, and use that to initialize an NSImage. I've used it when generating drag-and-drop images and it was acceptably fast, for a small area. If that doesn't work for NSView's that are not in a window, then you could create an offscreen window and stick it inside temporarily. If you reused the offscreen window every time it would probably not be a big performance penalty.*

----
You're right, the sample I posted above won't work but this one should...

    
// FadingWindow.h
#import <AppKit/AppKit.h>

@interface PHView : NSView
{
    id fadingWindow;
}
- (void)setFadingWindow:(id)win;
@end

@interface FadingWindow : NSWindow
{
    PHView *placeHolder;
}

// default initializer.
+ (id)fadingWindowWithView:(id)aView;

- (void)createPlaceHolder:(id)aView; // used internally
@end

@interface NSView (Fading)
- (float)alphaValue;
- (void)setAlphaValue:(float)alpha;
@end

    
// FadingWindow.m
#import "FadingWindow.h"

@implementation PHView

- (void)dealloc
{
    [fadingWindow release];
    [super dealloc];
}

- (BOOL)acceptsFirstResponder
{
    if (!fadingWindow) return NO;
    
    return fadingWindow contentView] acceptsFirstResponder];
}

- (BOOL)becomeFirstResponder
{
    if (!fadingWindow) return NO;
    
    BOOL returnVal = [[fadingWindow contentView] becomeFirstResponder];
    
    if (returnVal) {
        [fadingWindow makeFirstResponder:[fadingWindow contentView;
        [fadingWindow becomeKeyWindow];
    }
    
    return returnVal;
}

- (void)keyDown:(NSEvent *)theEvent
{
    if (!fadingWindow) return;
    
    fadingWindow contentView] keyDown:theEvent];
    
    if ([[theEvent characters] isEqualToString:@"	"]) { [super keyDown:theEvent]; }
}

- (void)setFadingWindow:(id)win
{
    [fadingWindow autorelease];
    fadingWindow = win;
    [fadingWindow retain];
}

@end


@implementation [[FadingWindow

+ (id)fadingWindowWithView:(id)aView
{
    FadingWindow *myself = [[FadingWindow alloc] init];

    [myself createPlaceHolder:aView];
    [myself setContentView:aView];
    myself->placeHolder window] addChildWindow:myself ordered:[[NSWindowAbove];
    [myself orderFront:nil];

    return [myself autorelease];
}

- (id)init
{
   self = [super initWithContentRect:NSMakeRect(0,0,1,1) styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:YES];

    [self setBackgroundColor:[NSColor clearColor]];
    [self setOpaque:NO];

    return self;
}

- (BOOL)makeFirstResponder:(NSResponder *)aResponder
{
   return [super makeFirstResponder:aResponder];
}

- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    [placeHolder release];
    self parentWindow] removeChildWindow:self];
    [super dealloc];
}

- (void)setContentView:(id)aView
{
    [[NSRect winRect = [aView frame];
    winRect.origin = aView window] convertBaseToScreen:winRect.origin];
    [self setFrame:winRect display:YES];
    [super setContentView:aView];
}

- (void)createPlaceHolder:(id)aView
{
    placeHolder = [[[[PHView alloc] initWithFrame:[aView frame]];
    [placeHolder setAutoresizingMask:[aView autoresizingMask]];
    [placeHolder setFadingWindow:self];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(frameChanged:) name:NSWindowDidResizeNotification object:[aView window]];
    aView superview] addSubview:placeHolder];
}

- (void)frameChanged:([[NSNotification *)note
{
    NSRect winRect = [placeHolder frame];
    winRect.origin = placeHolder window] convertBaseToScreen:winRect.origin];
    [self setFrame:winRect display:YES];
}

@end

@implementation [[NSView (Fading)
- (float)alphaValue
{
    return self window] alphaValue];
}

- (void)setAlphaValue:(float)value
{
    id window = [self window];
    
    if ([window isKindOfClass:[[[FadingWindow class]])
        [window setAlphaValue:value];
    else NSLog(@"%@ is not in a FadingWindow", self);
}
@end


And you can use it like so..
    
- (void)awakeFromNib
{
   [FadingWindow fadingWindowWithView:yourFadingView];
}

- (void)fadeOutTheView
{
   [NSTimer scheduledTimerWithTimeInterval:0.1
                                             target:self
                                           selector:@selector(fadeOutStep:)
                                           userInfo:nil
                                            repeats:YES];
}

- (void)fadeOutStep:(NSTimer *)timer
{
    float curAlpha = [yourFadingView alphaValue];
    if (curAlpha <= 0) { [timer invalidate]; return; }

    curAlpha -= 0.03;
    [yourFadingView setAlphaValue:curAlpha];
}


Note: You need to set the nextKeyView off your fade-able views in IB. The fade-able view will appear to retain firstResponder status if you don't.

That was a fun exercise! :-)

----
To whoever wrote the above, thanks. I had some trouble getting it implemented in a window
that has controls in a splitview and finally came up with the following "fix" (changes
shown in **bold**). All of these are applied in the above     F**'adingWindow implementation.

This seems like a bit of a hack to me, but I couldn't come up with
any     convertPoint or     convertBase... that worked -- controls in the right
frame kept getting placed relative to the window origin instead of the split frame
origin. If somebody has a better way, please post it. 
    
- (void)setContentView:(id)aView
{
    NSRect winRect = [aView frame];
    **NSPoint offset = [aView convertPoint:winRect.origin fromView:nil];**
    winRect.origin = aView window] convertBaseToScreen:winRect.origin];
    **winRect.origin = [[NSMakePoint(winRect.origin.x - offset.x,winRect.origin.y - offset.y);**
    [self setFrame:winRect display:YES];
    [super setContentView:aView];
}

- (void)createPlaceHolder:(id)aView
{
    placeHolder = [[PHView alloc] initWithFrame:[aView frame]];
    [placeHolder setAutoresizingMask:[aView autoresizingMask]];
    [placeHolder setFadingWindow:self];
    **[[NSNotificationCenter defaultCenter] addObserver:self 
                                          selector:@selector(frameChanged:) 
                                          name:NSSplitViewDidResizeSubviewsNotification 
                                          object:nil];
    // The following won't be necessary if your splitview resizes with the window
    // and you aren't using a toolbar.**
    [[NSNotificationCenter defaultCenter] addObserver:self 
                                          selector:@selector(frameChanged:) 
                                          name:NSWindowDidResizeNotification 
                                          object:[aView window]];
    aView superview] addSubview:placeHolder];
}

- (void)frameChanged:([[NSNotification *)note
{
    NSRect winRect = [placeHolder frame];
    **NSPoint offset = [placeHolder convertPoint:winRect.origin fromView:nil];**
    winRect.origin = placeHolder window] convertBaseToScreen:winRect.origin];
    **winRect.origin = [[NSMakePoint(winRect.origin.x - offset.x,winRect.origin.y - offset.y);**
    [self setFrame:winRect display:YES];
}


I'm not exactly thrilled with the need for two notifications, both of which fire when you resize the 
window (assuming your splitview is set up to resize with the window). If there were something like a
    N**'SToolBarDidHideOrShowNotification I could get around it, but no such luck.

-- PaulPomeroy

----
Any idea why the fading version of a segmented control would have a graphite control tint (instead of aqua) with the above code? Any idea how to fix it?
----
The original author would be RyanStevens. I'm just glad someone is finding the code useful. :-)

Paul, I would grab the toolbar button and use a pass-through...
    
SEL origAction;
id origTarget;
{
id button = [myWindow standardWindowButton:NSWindowToolbarButton];
    origAction = [button action];
    origTarget = [button target];

    [button setAction:@selector(pillPassThrough:)];
    [button setTarget:self];
}

- (void)pillPassThrough:(id)sender
{
   // send a notification 
   [sender sendAction:origAction to:origTarget];
}


As for the control tint, no clue.

----

Thanks again. Ends up I just subclassed NSToolBar and overrode the     -(void)setVisible:(BOOL)shown method to post a notification. I've hit one more related issue, though. I'm trying to get mouse entered/exited events for my fading controls (so I can "unfade" them when the pointer is over them). The controls are in a view which is actually what I'm handing over to the fade code. The mouse entered/exited notifications are working fine but I'm getting "exited" messages when the pointer moves from inside the containing view over one of the controls in it (and mouse entered when moved off the control back in to the containing view). There's something about how you've done this fading magic that I'm not following, I guess, but I can't seem to get around this. Suggestions? -Paul

----
Entered/exit messages are inverted?

Are you using tracking rects with the frames of your controls? I don't see how it could be inverted - there's no real "magic" in FadingWindow. The only tricky bit is how it sticks itself into the parent window's responder chain (which still needs to be fixed so that shift+tab works).

*The tracking rects are on the "containing views" (the views that are superviews to the actual controls, which are the views I've set up to fade). I get a mouseEntered when I get over one of these containing views (so far, so good) but as soon as the mouse gets over one of the contained controls I get a mouseExited (bad news). What it's behaving like is that the controls have become holes in the containing view (if that makes any sense). If I set up the same tracking scenario on these views but don't make them fading views things work as expected -- once inside a containing view there's no exited message when the pointer moves over a contained control. I could set up tracking rects for each control, but I really want to unfade them when the mouse gets into the containing view. This has to do with the app I'm working on (i.e., I'm not just pursuing this for the "fun" of it). Not sure if this has helped clarify things or not ...*  -Paul

Ok, I get it now - there is a hole...

Your fading views are really in their own windows, ordered above their original window. The problem is that when you mouse into a fading view you're also mousing into a different window and out of any tracking rects.

As a workaround I'd do the tracking rect setup in FadingWindow so each fading view gets one.

If only I could find work coding, then it would be for fun and profit! Anyone hiring? -RyanStevens (who always forgets to sign his posts)

*Thanks. I finally got the look & feel out of this I wanted... (well, almost - there's still the issue of why the controls lose their aqua tint as soon as the fading views are created...). About the tracking issue, I'm catching the mouseEntered event in the fading view and (if it's currently faded) unfade it and then post a notification for the main program. The main program then starts up a timer and checks every 2 seconds to see if the mouse is still in the general vicinity of the controls and, if not, refades them. Getting mouse points and view rects all "on the same page" is a real exercise in patience! Anyway, it ain't exactly pretty code, but it works.  Wish I could help you out on the job front ... I'm kind of in the "start up" phase. I sure could use the help (12-16 hour days is about all I can handle and only half of what I need to be doing right now) but can't afford to take anyone on (yet).*

----

NSViewFade has a version of this that doesn't need a helper NSWindow object, works properly if you're in full screen mode, but unfortunately requires Tiger.

