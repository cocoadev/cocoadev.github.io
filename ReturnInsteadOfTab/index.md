---
layout: page
title: ReturnInsteadOfTab
---

NSTextField responds to TAB to move to next field on the layout.
How easy is it to make it respond to RETURN ?
Or to respond to both Return OR tab ?

Database users often instinctively hit RETURN to leave a field.
Others are used to TAB.

----

I�d like to be able to manage 2 responder chains: the normal one, accessed by hitting Tab or Shift-Tab and a custom one triggered by hitting Return or Shift-Return.

The Tab chain might be set up in IB. And I�m trying to handle navigation too when the user exits the textField by hitting Return. I added some code in the Action method of the field but I can never make first responder the textField I want�

So I tried to override - (BOOL)textView:(NSTextView *)aTextView doCommandBySelector:(SEL)aSelector. I can catch that the event is NSTabCharacter for example but then when attempting to do aTextView window] selectKeyViewFollowingView:aTextView]; nothing happens�
Also [[NSLog(@"= NSTabCharacter nextKey = %@", [aTextView nextValidKeyView]); returns null even if the responder chain and the initial first responder were set in IB.

So here is my question: Has anyone done this before and is able to submit a How-To?? What would be the better strategy: the action method or what else?

Also, if we use the action method, how (and when) do we check for Shift-Return instead of Return?

----
God alone knows why you might want to do this. Two responder chains is terribly non-standard, and no one -- including the full keyboard access and speech recognition features -- will know about it.

That said, I suggest you do the first responder like Apple does the first responder. Make a subclass of NSWindow and intercept the return and shift-return events at that level. NSWindow manages events dispatch and focus.
----
But in which method should I intercept those events?? And how??

[I actually have some textfields and I just want to be able to go through them tabbing or to cycle through only some of them if I decide to press Return.]
----
So, it works with the code below� (Shift-Return doesn�t work, though�)

I made 2 columns of 4 texFields and am now able to tab horizontally through them (normally using Tab: 0-1-2-3-4-5-6-7-0�) or vertically (using Return: 0-2-4-6-0� or 1-3-5-7-1�).

I created a custom object that stores every TF�s next and previous responder.
An array of such objects is build in the controller�s awakeFromNib.
You just cycle through that array to find the current view�s next "return-responder", recursively if it turns up to be invisible�

**The custom object .h:**
    
#import <Foundation/Foundation.h>


@interface ResponderObject : NSObject {
    
    NSView * resp;
    NSView * previousResp;
    NSView * nextResp;
    
}

// Accessor Methods
- (NSView*)resp;
- (void)setResp:(NSView*)aResp;

- (NSView*)previousResp;
- (void)setPreviousResp:(NSView*)aResp;

- (NSView*)nextResp;
- (void)setNextResp:(NSView*)aResp;

@end



**Controller.h:**
    
#import <Foundation/Foundation.h>

@interface Controller : NSObject {
    
    IBOutlet NSTextField * tf0;
    IBOutlet NSTextField * tf1;
    IBOutlet NSTextField * tf2;
    IBOutlet NSTextField * tf3;
    
    IBOutlet NSTextField * tf4;
    IBOutlet NSTextField * tf5;
    IBOutlet NSTextField * tf6;
    IBOutlet NSTextField * tf7;
    
    NSArray * respChainArray; // Our own responder chain!
}

- (IBAction)vertTab:(id)sender;

@end

**Controller.m:**
    
#import "Controller.h"
#import "ResponderObject.h"

@implementation Controller

- (void)awakeFromNib
{ 
    int i;
    
    // Array to cycle through all the textFields IBOutlets.
    NSArray * tfArray = [[NSArray alloc] initWithObjects:
	tf0, tf1, tf2, tf3, tf4, tf5,tf6, tf7, nil];
    
    // Set Action, Target and sendActionOn for the tFs.
    for (i = 0; i <= 7; i++) {
	tfArray objectAtIndex:i] setAction:@selector(vertTab:)];
	[[tfArray objectAtIndex:i] setTarget:self];
	[[[tfArray objectAtIndex:i] cell]setSendsActionOnEndEditing:NO];
    }
    
    // Temporary array to fill our responder chain array with.
    [[NSMutableArray * tempRCArray = [[NSMutableArray alloc] init];
    
    for (i = 0; i <= 7; i++) { // Build our responder chain.
	
	FLResponderObject * theRespObj = [[FLResponderObject alloc] init];
	
	[theRespObj setResp:[tfArray objectAtIndex:i]];
	
	// Cases 0, 1, 6 and 7 to loop to the beginning/end of the column.
	if (i == 6) [theRespObj setNextResp:[tfArray objectAtIndex:0]];
	else if (i == 7) [theRespObj setNextResp:[tfArray objectAtIndex:1]];
	else [theRespObj setNextResp:[tfArray objectAtIndex:i+2]];
	
	if (i == 0) [theRespObj setPreviousResp:
	    [tfArray objectAtIndex:6]];
	else if (i == 1) [theRespObj setPreviousResp:
	    [tfArray objectAtIndex:7]];
	else [theRespObj setPreviousResp:[tfArray objectAtIndex:i-2]];
	
	[tempRCArray insertObject:theRespObj atIndex:i];
	
	[theRespObj release];
	
    }
    
    respChainArray = [[NSArray alloc] initWithArray:tempRCArray];
    [tempRCArray release];
    [tfArray release];
    
}

- (IBAction)vertTab:(id)sender;
{ // Action of the TFs to be able to "Tab Vertically" (using the return key).
    int i;
    NSView * newResp = nil;
    
    for (i = 0; i <= 7; i++) { // Find out our own nextResponder.
	if ([sender isEqualTo:respChainArray objectAtIndex:i] resp) {
	    newResp = respChainArray objectAtIndex:i] nextResp];
	    break; }
    }
    
    while ([newResp isHidden]) { // While it isn�t Visible,
	for (i = 0; i <= 7; i++) {  //take its nextResponder.
	    if ([newResp isEqualTo:[[respChainArray objectAtIndex:i] resp) {
		newResp = respChainArray objectAtIndex:i] nextResp];
		break; }
	}
    }
    [[sender window] makeFirstResponder:newResp];
}

- ([[IBAction)setTFHidden:(id)sender;
{ // Action for the checkboxes to make the textFields hidden.
    [ [sender window] contentView] viewWithTag:[sender tag setHidden:[sender state]];
}

@end

