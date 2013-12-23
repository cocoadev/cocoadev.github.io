---
layout: page
title: FakeImageView
---

I wrote FakeImageView to give me true proportional scaling and a few other features. 

FakeImageView.h:
    
#import <AppKit/AppKit.h>

@interface FakeImageView : NSView
{
    NSImage *_image;
    NSColor *_bgColor;
    NSImageScaling _scaling;
}
- (void)setImage:(NSImage*)image;
- (void)setImageScaling:(NSImageScaling)newScaling;
- (void)setBackgroundColor:(NSColor*)color;
- (NSImage*)image;
- (NSColor*)backgroundColor;
- (NSImageScaling)imageScaling;
@end


FakeImageView.m
    
#import "FakeImageView.h"

@implementation FakeImageView

- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        _bgColor = [[NSColor blackColor] retain];
        _scaling = NSScaleProportionally;
    }

    return self;
}

- (void)dealloc
{
    [_image release];
	[_bgColor release];
    [super dealloc];
}

- (void) setImage:(NSImage*)image
{
    if (_image) {
        [_image autorelease];
        _image = nil;
    }

    _image = [image retain];
    [_image setScalesWhenResized:YES];
    [self setNeedsDisplay:YES];
}

- (NSImage*)image
{
    return _image;
}

- (void)setImageScaling:(NSImageScaling)newScaling
{
    _scaling = newScaling;
    [self setNeedsDisplay:YES];
}

- (NSImageScaling)imageScaling
{
    return _scaling;
}

- (void)setBackgroundColor:(NSColor*)color
{
    if (_bgColor) {
        [_bgColor autorelease];
        _bgColor = nil;
    }

    _bgColor = [color retain];
    [self setNeedsDisplay:YES];
}

- (NSColor*)backgroundColor
{
    return _bgColor;
}

- (void) drawRect:(NSRect)rects
{
    NSRect bounds = [self bounds];

    [_bgColor set];
    NSRectFill(bounds);
    
    if (_image) {
        NSImage *copy = [_image copy];
        NSSize size = [copy size];
        float rx, ry, r;
        NSPoint pt;

        switch (_scaling) {
            case NSScaleProportionally:
                rx = bounds.size.width / size.width;
                ry = bounds.size.height / size.height;
                r = rx < ry ? rx : ry;
                size.width *= r;
                size.height *= r;
                [copy setSize:size];
                break;
            case NSScaleToFit:
                size = bounds.size;
                [copy setSize:size];
                break;
            case NSScaleNone:
                break;
            default:
                ;
        }

        pt.x = (bounds.size.width - size.width) / 2;
        pt.y = (bounds.size.height - size.height) / 2;
        
        [copy compositeToPoint:pt operation:NSCompositeCopy];
        [copy release];
    }
}

@end


When necessary, I create a FakeImageView subclass of NSView in IB and set image views to that custom class. Maybe it would have been better to subclass NSImageView ... but oh well. Your mileage may vary.

-- MikeTrent

----
Here is how to add Drag and Drop support to this class (from finder).
Insert this into the init function:


    

    [self registerForDraggedTypes:[NSArray arrayWithObject:NSFilenamesPboardType]];


Then implement the following methods:
    

- (unsigned int)draggingEntered:(id <NSDraggingInfo>)sender {
        if (sender draggingPasteboard] availableTypeFromArray:[[[NSArray arrayWithObject:NSFilenamesPboardType]]) {
            return NSDragOperationCopy;
        }
return NSDragOperationNone;
}

- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender {
    NSPasteboard *pb = [sender draggingPasteboard];
    NSString *type = [pb availableTypeFromArray:[NSArray arrayWithObject:NSFilenamesPboardType]];
    NSArray *array = pb stringForType:type] propertyList];
    [[NSImage *testImage;
    // Try to make a Image out of first filename on pasteboard:
    if (testImage = [[NSImage alloc] initWithContentsOfFile:[array objectAtIndex:0]])
    {
        [testImage release];
        [self setImage:testImage];
        return YES;
    }
    return NO;
}

- (BOOL)prepareForDragOperation:(id <NSDraggingInfo>)sender {
    return YES;
}


If you want to implement archiving support for this class change the @Implementation line to look like this:
    
@interface FakeImageView : NSView <NSCoding>

And add the following methods:
    
-(void)encodeWithCoder:(NSCoder *)coder
{
[super encodeWithCoder:coder];
[coder encodeObject:_image];
[coder encodeObject:_bgColor];
[coder encodeValueOfObjCType:@encode(NSImageScaling) at:&_scaling];

}

-(id)initWithCoder:(NSCoder *)coder
{
if (self = [super initWithCoder:coder])
    {
        [self setImage:[coder decodeObject]];
        [self setBackgroundColor:[coder decodeObject]];
        [coder decodeValueOfObjCType:@encode(NSImageScaling) at:&_scaling];

    }
return self;
}


Any optimizations encouraged :)

Also I suggest replacing NSRectFill(bounds); with [NSBezierPath fillRect: [self bounds]]; because the latter allows you to set background to clearColor.

GormanChristian

----
Some suggestions to clean up the mutator calls a bit: http://goo.gl/OeSCu
    
- (void) setImage:(NSImage*)image
{
    NSImage *temp = [image retain];

    [_image release];
    _image = temp;
    [_image setScalesWhenResized:YES];
    [self setNeedsDisplay:YES];
}

- (void)setBackgroundColor:(NSColor*)color
{
    NSColor *temp = [color retain];

    [_bgColor release];
    _bgColor = temp;
    [self setNeedsDisplay:YES];
}


Saves you a few autorelease pool dependencies which could make debugging trickier (I hate it when something crashes outside of the event loop because of an autorelease mistake).  It also makes the code a little cleaner.

Also, a bit of a technicality, but there should probably be a super call in the drawRect: method.  Since the background is being redrawn by this code it might not matter but it is always nice in case the NSView implementation in the future wants to do something else.

Almost forgot that this will require that you set _image to nil in the initWithFrame: method (and the initWithCoder: if you are using that).

Someone else can insert these modifications in place if they want to but I didn't want to step on any toes in case you guys had some reason for doing it this way.

-Jeff.

----

Here is some code to allow the use of the rest of the image drag and drop destination functionality.  I haven't tested it all myself since I don't have any pict or eps on my drive but it should work.

Firstly, the initWithFrame:
    
- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (nil != self)
    {
        _bgColor = [[NSColor blackColor] retain];
        _scaling = NSScaleProportionally;
        _image = nil;
        [self registerForDraggedTypes:[NSArray arrayWithObjects:NSPostScriptPboardType,
                                                       NSPICTPboardType, NSPDFPboardType,
                                                       NSFileContentsPboardType, NSTIFFPboardType,
                                                       NSFilenamesPboardType, nil]];
    }
    return self;
}


Then the performDragOperation:
    
- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender
{
    NSPasteboard *paste = [sender draggingPasteboard];
    NSArray *types = [NSArray arrayWithObjects: NSPostScriptPboardType, NSPICTPboardType,
                                                NSPDFPboardType, NSFileContentsPboardType,
                                                NSTIFFPboardType, NSFilenamesPboardType, nil];
    NSString *desiredType = [paste availableTypeFromArray:types];
    NSImage *newImage = nil;

    if ([desiredType isEqualToString:NSFilenamesPboardType])
    {
        NSArray *fileArray = [paste propertyListForType:@"NSFilenamesPboardType"];
        NSString *path = [fileArray objectAtIndex:0];
        newImage = [[NSImage alloc] initWithContentsOfFile:path];
    
        if (nil == newImage)
        {
            //complain since it failed (panel or something)
            return NO;
        }
    }
    else
    {
        NSData *carriedData = [paste dataForType:desiredType];

        if (nil == carriedData)
        {
            //data was invalid so complain
            return NO;
        }
        else
        {
        newImage = [[NSImage alloc] initWithData:carriedData];
            if (nil == newImage)
            {
                return NO;
            }
        }
    }
    [self setImage:newImage];
    [newImage release];    
    [self setNeedsDisplay:YES];    //redraw us with the new image
    return YES;
}


That should work.  More information on Drag and Drop Destinations can be found in this tutorial:
http://cocoadevcentral.com/articles/000056.php

-Jeff.

----
See also: NSImageViewIsJustKidding

----

Why doesn't this page wrap properly (in mozilla)?

Because of the HTML preformatted text tag. It doesn't wrap. We're (sort of) working on it... -- RobRix

Easiest way to fix that is to manually wrap the long line ... -- SimonWoodside

Did it. -- UliKusterer

----

*Wouldn't it be more efficient to apply an NSAffineTransform to the view rather than copy the whole image?*

You tell me. Implement the change, measure its performance, and post the results. -- MikeTrent

----

I didn't make the suggestion, but I implemented image scaling and translation (user dragging the image around the view) with NSAffineTransform and it's incredibly fast.  I don't have hard numbers, but I was able to update the image so quickly I could see screen tearing when dragging the image around the view. On my dual 2.0, both processors were hovering round 25-30% while dragging continuously using NSAffineTransform as opposed to 50-60% for the copy.

I actually had to slow it down and only '[self setNeedsDisplay: YES];' every _other_ call to mouseDragged, it was too fast.

----

Visible screen-tearing doesn't tell you much about the code's speed - in fact it could indicate the redraw is slow. All it means is that the video scan overtook the redraw of the graphic in question. If that graphic moved a noticeable distance between one repaint and the next, it will look torn. So the amount of tearing can be related to the speed at which you move the mouse! In fact if the graphics are updating very fast, the amount that the mouse can have moved between frames will be less, so the tearing will be less - therefore noticeable tearing = slow updates. The fact is this isn't a reliable indicator of performance. The only way to know for sure is to profile the two approaches under identical conditions (which probably means driving the motion automatically rather than manual mouse dragging). 
----

if you want the same apearance as with NSImageView, use NSCompositeSourceOver instead of NSCompositeCopy

----

I think this would be better as a category. Maybe modify the first category on the NSImageCategory page so that it can do either type of scaling. Then you could use a normal NSImageView for display and that first category would actually be useful.

*I figured since I suggested it I might as well just do it. You can always revert NSImageCategory if it's not ok to borrow. :)*

