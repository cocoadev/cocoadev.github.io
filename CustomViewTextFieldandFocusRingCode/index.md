---
layout: page
title: CustomViewTextFieldandFocusRingCode
---


     
#import "KeyView.h"

@implementation KeyView

- (id)initWithFrame:(NSRect)frameRect
{
        [super initWithFrame:frameRect];
	return self;
}


-(void)dealloc {
	[nonVisibleDict release];
	[self release];
}

-(void)awakeFromNib {

	NSArray *keys=[NSArray arrayWithObjects:@"76",@"36",@"122",@"120",@"99",@"118",
@"96",@"97",@"98",@"100",@"101",@"109",@"103",@"111",
@"124",@"123",@"125",@"126",@"51",@"53",@"49",@"48",nil];
        NSArray *objects=[NSArray arrayWithObjects:@"Enter",@"Return",@"F1",@"F2",@"F3"
,@"F4",@"F5",@"F6",@"F7",@"F8",
@"F9",@"F10",@"F11",@"F12",@"Right Arrow",@"Left Arrow",@"Down Arrow",
@"Up Arrow",@"Delete",@"Escape",@"Space",@"Tab",nil];
        
        nonVisibleDict=[[[NSDictionary alloc] initWithObjects:objects forKeys:keys] retain];
}

- (BOOL) becomeFirstResponder
{
        isSelected=YES;
        [self setNeedsDisplay:YES];
        return YES;
   
}

- (BOOL)resignFirstResponder {
    isSelected=NO;
    [self setNeedsDisplay:YES];
    return YES;
}


- (void)drawRect:(NSRect)rect
{

        NSBezierPath *myPath=[NSBezierPath bezierPathWithRect:rect];
        [[NSColor whiteColor] set];
        [myPath fill];
    
       if ([viewString isEqualToString:@""] !=YES) {
            [[NSColor blackColor] set];
            [viewString drawAtPoint:NSMakePoint(7,NSMidY(rect)-8) withAttributes:nil];
        }	

        if (isSelected==YES && [myWindow isKeyWindow]) {
            NSSetFocusRingStyle(NSFocusRingOnly); 
            NSRectFill(rect);
            [self setKeyboardFocusRingNeedsDisplayInRect: rect];
        } else {
            myPath=[NSBezierPath bezierPathWithRect:rect];
            [[NSColor grayColor] set];
            [myPath stroke]; 
        }
    
}

- (BOOL)acceptsFirstResponder { 
    return YES;
} 

- (void)mouseDown:(NSEvent *)theEvent {
    isSelected=YES;
    [self setNeedsDisplay:YES];
}



- (void)keyDown:(NSEvent *)theEvent {
  if (isSelected=YES) {
        unichar myKeystroke = [[[NSApp currentEvent] charactersIgnoringModifiers]
characterAtIndex:0]; 
        myKeyCode = [[NSApp currentEvent] keyCode];
    
        [viewString release];
        
        if ([nonVisibleDict objectForKey:[[NSNumber numberWithInt:myKeyCode]
stringValue]] != nil) {
            viewString=[[NSString stringWithString:
[nonVisibleDict objectForKey:[[NSNumber numberWithInt:myKeyCode] stringValue]]] retain];
        } else {
            viewString=[[NSString stringWithFormat:@"%C",myKeystroke] retain];
        }
      
        
        [self setNeedsDisplay:YES]; 
     }

}

@end
 

