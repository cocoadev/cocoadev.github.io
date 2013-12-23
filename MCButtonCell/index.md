---
layout: page
title: MCButtonCell
---

For explaination and sample use see: ClickThroughButtonInTableView

    


//  2005 by St�phane BARON - MacAvocat .
//  Derivated from work by Erik Doernenburg
//  Derivated from work by Omnigroup
//  Derivated from work by Eric Petit
//
//  Permission is hereby granted, free of charge, to any person obtaining a
//  copy of this software and associated documentation files (the "Software"),
//  to deal in the Software without restriction, including without limitation
//  the rights to use, copy, modify, merge, publish, distribute, sublicense,
//  and/or sell copies of the Software, and to permit persons to whom the
//  Software is furnished to do so, subject to the following conditions:
//
//  The above copyright notice and this permission notice shall be included in
//  all copies or substantial portions of the Software.
// 
//  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
//  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
//  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
//  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
//  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
//  FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
//  DEALINGS IN THE SOFTWARE.
 
#import <Cocoa/Cocoa.h>

@interface MCButtonCell : NSActionCell { 
	
	NSRect imageRect;
	NSMutableDictionary *theAttributes;
	NSImage *currentImage;
	NSImage *image;
	NSImage *alternateImage;
//	NSImage *rolloverImage;
	
}

-(NSImage *)image;
-(void)setImage:(NSImage *)anImage;
-(NSImage *)alternateImage;
-(void)setAlternateImage:(NSImage *)anImage;
//-(NSImage *)rolloverImage;
//-(void)setRolloverImage:(NSImage *)anImage;
-(NSImage *)currentImage;
-(void)setCurrentImage:(NSImage *)anImage;
-(BOOL)isPointInImageRect:(NSPoint)location forCell:(NSRect)cellFrame;
- (void) mouseDown: (NSEvent *)theEvent;
- (void) mouseUp: (NSEvent *)theEvent;


@end


#import "MCButtonCell.h"

#define TEXT_VERTICAL_OFFSET (-1.0)
#define FLIP_VERTICAL_OFFSET (-9.0)
#define BORDER_BETWEEN_EDGE_AND_IMAGE (3.0)
#define BORDER_BETWEEN_IMAGE_AND_TEXT (3.0)
#define SIZE_OF_TEXT_FIELD_BORDER (1.0)

static NSMutableParagraphStyle *MCTParagraphStyle = nil;
NSString *MCTStringKey = @"string";

@implementation MCButtonCell



-(id)init
{
	
    if ([super initTextCell:@""] == nil)
        return nil;
	return self;
	
}

-(void)awakeFromNib;
{
	
	[self setCurrentImage:image];
	
}

-(BOOL)isPointInImageRect:(NSPoint)location forCell:(NSRect)cellFrame;
{
	NSSize theSize=[currentImage size];
	if((location.x>theSize.width+cellFrame.origin.x) || (location.x<BORDER_BETWEEN_EDGE_AND_IMAGE+cellFrame.origin.x))
	{
		return NO;
	}
	else
		return YES;
}


-(NSSize)imageSize;
{
return [currentImage size];
}

-(NSImage *)image;
{
	return image;
}

-(void)setImage:(NSImage *)anImage;
{
    if (anImage == image)
        return;
    [image release];
    image = [anImage retain];
}

-(NSImage *)currentImage;
{
return currentImage;
}

-(void)setCurrentImage:(NSImage *)anImage;
{
    if (anImage == currentImage)
        return;
    [currentImage release];
    currentImage = [anImage retain];
}

-(NSImage *)alternateImage;
{
return alternateImage;
}

-(void)setAlternateImage:(NSImage *)anImage;
{
    if (anImage == alternateImage)
        return;
    [alternateImage release];
    alternateImage = [anImage retain];
}

- (void)setControlView:(NSView*)view
{
[super controlView];
}

- (void)drawInteriorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{

if(currentImage==nil)
{
[self setCurrentImage:image];
}


	NSPoint        point;
	NSSize        size;
	NSPoint        origin;	
	origin = cellFrame.origin;
	imageRect=NSMakeRect(origin.x+BORDER_BETWEEN_EDGE_AND_IMAGE,origin.y,[currentImage size].width,[currentImage size].height);
	NSRect textRect=NSMakeRect(origin.x+[currentImage size].width+BORDER_BETWEEN_EDGE_AND_IMAGE+BORDER_BETWEEN_IMAGE_AND_TEXT,origin.y,cellFrame.size.width-([currentImage size].width+BORDER_BETWEEN_EDGE_AND_IMAGE+BORDER_BETWEEN_IMAGE_AND_TEXT),cellFrame.size.height);

    textRect = NSInsetRect(textRect, 1.0, 0.0);

    if (![controlView isFlipped])
        textRect.origin.y -= (textRect.size.height + FLIP_VERTICAL_OFFSET);


	if (currentImage != nil)
       {
       size = [currentImage size];
       point.x = origin.x+BORDER_BETWEEN_EDGE_AND_IMAGE;
       point.y = origin.y+size.height+((cellFrame.size.height-size.height)/2);
       [currentImage compositeToPoint: point operation:  NSCompositeSourceOver];
       point.x = point.x + size.width;
       }
   else
       {
       point.x = origin.x;
       }
   point.y = origin.y;


	NSMutableAttributedString *label = [[NSMutableAttributedString alloc] initWithAttributedString:[self attributedStringValue]];
    NSRange labelRange = NSMakeRange(0, [label length]);
    if ([NSColor respondsToSelector:@selector(alternateSelectedControlColor)]) 
	{
        NSColor *highlightColor = [self highlightColorWithFrame:cellFrame inView:controlView];
        BOOL highlighted = [self isHighlighted];

        if (highlighted && [highlightColor isEqual:[NSColor alternateSelectedControlColor]]) 
		{
            [label addAttribute:NSForegroundColorAttributeName value:[NSColor alternateSelectedControlTextColor] range:labelRange];
        }
    }

    [label addAttribute:NSParagraphStyleAttributeName value:MCTParagraphStyle range:labelRange];
    [label drawInRect:textRect];
    [label release];

}


- (void)setObjectValue:(id <NSObject, NSCopying>)obj;
{

    if ([obj isKindOfClass:[NSString class]] || [obj isKindOfClass:[NSAttributedString class]]) {
        [super setObjectValue:obj];
        return;
    } else if ([obj isKindOfClass:[NSDictionary class]]) {
        NSDictionary *dictionary = (NSDictionary *)obj;
        
        [super setObjectValue:[dictionary objectForKey:MCTStringKey]];
    }

}


- (void) mouseDown: (NSEvent *)theEvent;
{
[self setCurrentImage:alternateImage];
}

- (void) mouseUp: (NSEvent *)theEvent
{
[self setCurrentImage:image];
}

+ (void)initialize;
{
	MCTParagraphStyle = [[NSMutableParagraphStyle alloc] init];
    [MCTParagraphStyle setLineBreakMode:NSLineBreakByTruncatingTail];
}
@end



