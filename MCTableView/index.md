---
layout: page
title: MCTableView
---

*Please remember to properly format your pages! :-) Also, please post some sort of explanation of this class!!!*

Yes, it's done now, i was a bit too slow :-))

For sample use see: ClickThroughButtonInTableView

    
//  2005 by St�phane BARON - MacAvocat . All rights reserved.
//  Derivated from work by Erik Doernenburg
//  Derivated from work by Omnigroup, as i can remember.
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
 
 
#import <AppKit/NSTableView.h>
#import "MCButtonCell.h"

@interface MCTableView : NSTableView
{
	
	int clickedButtonNumber;
       int	clickedRowIdx, clickedColumnIdx;
	int previousRow;
	NSCell *previousCell;
	NSRect cellFrameDown;
	BOOL isClickedButton;
}


- (void)setClickedButton:(int)aButton;
- (void)mouseUp:(NSEvent *)theEvent;

@end



#import "MCTableView.h"
#import <AppKit/AppKit.h>
#import "MCButtonCell.h"

@implementation MCTableView


- initWithFrame:(NSRect)frame
{
    [super initWithFrame:frame];
	
    return self;
}


- (void)dealloc
{
    [super dealloc];
}


- (void)mouseDown:(NSEvent *)theEvent
{
    NSTableColumn	*column;
    NSCell			*cell;
    NSRect			cellFrame;
    NSPoint			location;
	
	location = [self convertPoint:[theEvent locationInWindow] fromView:nil];
    clickedRowIdx = [self rowAtPoint:location];
    clickedColumnIdx = [self columnAtPoint:location];        
	column = [_tableColumns objectAtIndex:clickedColumnIdx];
	cell = [column dataCell];
	
    if((clickedColumnIdx == -1) || (clickedRowIdx == -1) ||	([cell isKindOfClass:[MCTCell class]]==NO))
	{
		
		isClickedButton=NO;
		[self setClickedButton:-1];
        [super mouseDown:theEvent];
		
	}
    else
	{
		
		cellFrame = [self frameOfCellAtColumn:clickedColumnIdx row:clickedRowIdx];
		cellFrameDown=cellFrame;
		
		if([cell isPointInImageRect:location forCell:cellFrame])
		{
			
			isClickedButton=YES;
			previousRow=clickedRowIdx;
			previousCell=cell;
			
			[cell mouseDown:theEvent];
			[self displayRect:[self rectOfRow:clickedRowIdx]];
			
		}
		else
		{
			
			isClickedButton=NO;
			[self setClickedButton:-1];
			[super mouseDown:theEvent];
			
		}
	}
}

-(void)mouseUp:(NSEvent *)theEvent;
{
	
	NSTableColumn	*column;
    NSCell			*cell;
    NSRect			cellFrameUp;
    NSPoint			location;
	
	if(isClickedButton==NO)
	{
		[super mouseUp:theEvent];
	}
	else
	{	
		location = [self convertPoint:[theEvent locationInWindow] fromView:nil];
		clickedRowIdx = [self rowAtPoint:location];
		clickedColumnIdx = [self columnAtPoint:location];
		
		column = [_tableColumns objectAtIndex:clickedColumnIdx];
		
		cell = [column dataCell];
		
		
		if((clickedColumnIdx == -1) || (clickedRowIdx == -1) ||([cell isKindOfClass:[MCTCell class]]==NO))
        {
			[self setClickedButton:-1];
			[previousCell mouseUp:theEvent];
			[self displayRect:[self rectOfRow:previousRow]];
			
			isClickedButton=NO;
		}
		else
        {
			cellFrameUp = [self frameOfCellAtColumn:clickedColumnIdx row:clickedRowIdx];
			
			if(NSEqualRects(cellFrameUp,cellFrameDown))
			{
				
				if([cell isPointInImageRect:location forCell:cellFrameUp])
				{
					[cell mouseUp:theEvent];
					[self displayRect:[self rectOfRow:clickedRowIdx]];					
					[self setClickedButton:clickedRowIdx];
					self target] performSelector:[self action] withObject:self];
					isClickedButton=NO;
				}
				else
				{
					[self setClickedButton:-1];
					[previousCell mouseUp:theEvent];
					[self displayRect:[self rectOfRow:previousRow;
					isClickedButton=NO;
					
				}
			}
			
		}
	}
}

- (int)clickedColumn
{
    return clickedColumnIdx;
}

- (int)clickedRow
{
    return clickedRowIdx;
}

-(void)awakeFromNib;
{
	
	[self setRowHeight:17.0];
	isClickedButton=NO;
	
	
	
}

- (int)clickedButton;
{
    return clickedButtonNumber;
}

- (void)setClickedButton:(int)aButton;
{
    if (aButton == clickedButtonNumber)
        return;
    clickedButtonNumber=nil;
    clickedButtonNumber =aButton;
}

@end

