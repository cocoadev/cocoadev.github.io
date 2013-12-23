---
layout: page
title: NSAlertCheckbox
---

I got annoyed that NSAlert doesn't have any SIMPLE way to add a checkbox to it, so I wrote this custom subclass to add it in, enjoy:

NSAlertCheckbox.h:
    
#import <Cocoa/Cocoa.h>

// private methods
@interface NSAlert (CheckboxAdditions)
- (void)prepare;
- (id)buildAlertStyle:(int)fp8 title:(id)fp12 formattedMsg:(id)fp16 first:(id)fp20 second:(id)fp24 third:(id)fp28 oldStyle:(BOOL)fp32;
- (id)buildAlertStyle:(int)fp8 title:(id)fp12 message:(id)fp16 first:(id)fp20 second:(id)fp24 third:(id)fp28 oldStyle:(BOOL)fp32 args:(char *)fp36;
@end

@interface NSAlertCheckbox : NSAlert {
	NSButton *_checkbox;
}

+ (NSAlertCheckbox *)alertWithMessageText:(NSString *)message defaultButton:(NSString *)defaultButton alternateButton:(NSString *)alternateButton otherButton:(NSString *)otherButton informativeText:(NSString *)format;

- (BOOL)showsCheckbox;
- (void)setShowsCheckbox:(BOOL)showsCheckbox;

- (NSString *)checkboxText;
- (void)setCheckboxText:(NSString *)text;

- (int)checkboxState;
- (void)setCheckboxState:(int)state;
@end


NSAlertCheckbox.m:
    
#import "NSAlertCheckbox.h"

@interface NSAlertCheckbox (Private)
- (void)_addCheckboxToWindow;
@end

@implementation NSAlertCheckbox
- (id)init {
	if (self = [super init]) {
		_checkbox = nil;
	}
	
	return self;
}

- (void)dealloc {
	[_checkbox release];
	
	[super dealloc];
}

+ (NSAlertCheckbox *)alertWithMessageText:(NSString *)message defaultButton:(NSString *)defaultButton alternateButton:(NSString *)alternateButton otherButton:(NSString *)otherButton informativeText:(NSString *)format {
	NSAlert *alert = [super alertWithMessageText:message
								   defaultButton:defaultButton
								 alternateButton:alternateButton
									 otherButton:otherButton
					   informativeTextWithFormat:format];
	
	return (NSAlertCheckbox *)alert;
}

#pragma mark -

- (BOOL)showsCheckbox {
	return (_checkbox != nil);
}

- (void)setShowsCheckbox:(BOOL)showsCheckbox {
	if (showsCheckbox && !_checkbox) {
		_checkbox = [[NSButton alloc] initWithFrame:NSZeroRect];
		[_checkbox setButtonType:NSSwitchButton];
	} else if (!showsCheckbox && _checkbox) {
		if ([_checkbox superview]) {
			[_checkbox removeFromSuperview];
		}
		[_checkbox release];
		_checkbox = nil;
	}
}

- (NSString *)checkboxText {
	NSString *text = nil;
	
	if ([self showsCheckbox]) {
		text = [_checkbox title];
	}
	
	return text;
}

- (void)setCheckboxText:(NSString *)text {
	if ([self showsCheckbox]) {
		[_checkbox setTitle:text];
	}
}

- (int)checkboxState {
	int state = -1;
	
	if ([self showsCheckbox]) {
		state = [_checkbox state];
	}
	
	return state;
}

- (void)setCheckboxState:(int)state {
	if ([self showsCheckbox]) {
		[_checkbox setState:state];
	}
}

#pragma mark -

- (id)buildAlertStyle:(int)fp8 title:(id)fp12 formattedMsg:(id)fp16 first:(id)fp20 second:(id)fp24 third:(id)fp28 oldStyle:(BOOL)fp32 {
	id retVal = [super buildAlertStyle:fp8 title:fp12 formattedMsg:fp16 first:fp20 second:fp24 third:fp28 oldStyle:fp32];
	[self _addCheckboxToWindow];
	
	return retVal;
}

- (id)buildAlertStyle:(int)fp8 title:(id)fp12 message:(id)fp16 first:(id)fp20 second:(id)fp24 third:(id)fp28 oldStyle:(BOOL)fp32 args:(char *)fp36 {
	id retVal = [super buildAlertStyle:fp8 title:fp12 message:fp16 first:fp20 second:fp24 third:fp28 oldStyle:fp32 args:fp36];
	[self _addCheckboxToWindow];
	
	return retVal;
}
@end

@implementation NSAlertCheckbox (Private)
- (void)_addCheckboxToWindow {
	float checkboxPadding = 14.0f; // according to the apple HIG
	
	if ([self showsCheckbox]) {
		NSWindow *window = [self window];
		NSView *content = [window contentView];
		
		// find the position of the main text field
		NSArray *subviews = [content subviews];
		NSEnumerator *en = [subviews objectEnumerator];
		NSView *subview = nil;
		NSTextField *messageText = nil;
		int count = 0;
		
		while (subview = [en nextObject]) {
			if ([subview isKindOfClass:[NSTextField class]]) {
				count++;
				
				if (count == 2) {
					messageText = (NSTextField *)subview;
				}
			}
		}
		
		if (messageText) {
			[content addSubview:_checkbox];
			
			// make the checkbox font match the text area above it
			[_checkbox setFont:[messageText font]];
			[_checkbox sizeToFit];
			
			// expand the window
			NSRect windowFrame = [window frame];
			NSRect checkboxFrame = [_checkbox frame];
			windowFrame.size.height += checkboxFrame.size.height + checkboxPadding;
			[window setFrame:windowFrame display:YES];
			
			checkboxFrame.origin.y = [messageText frame].origin.y - checkboxFrame.size.height - checkboxPadding;
			checkboxFrame.origin.x = [messageText frame].origin.x;
			
			[_checkbox setFrame:checkboxFrame];
			[window makeFirstResponder:self buttons] objectAtIndex:0;
		}
	}
}
@end


Here's some example code using it:
    
// prompt the user
NSAlertCheckbox *alert = [NSAlertCheckbox alertWithMessageText:@"Install Flash"
								 defaultButton:@"Download"
							   alternateButton:@"No Thanks"
								   otherButton:nil
							   informativeText:@"The current verson of flash is out of date, do you wish to download the latest?"];
		
[alert setShowsCheckbox:YES];
[alert setCheckboxText:@"Don't ask me again."];
[alert setCheckboxState:NSOnState];

if ([alert runModal] == NSAlertDefaultReturn) {
	// go download flash
}

if ([alert checkboxState] == NSOnState) {
	// go set a pref somewhere
}


-- TristanOTierney

----

I've got some similar code that will use the private checkbox methods built into NSAlert, if they're available, or fallback to a regular checkbox if the code appears to be missing. The advantage to this code is that there is less direct manipulation of the alert's window and controls--i.e. it's a higher-level implementation. If people would like, I can clean it up and post it as a download. - JonathanGrynspan

----
TristanOTierney thanks and JonathanGrynspan, please do. 

----
Looks like Leopard allows you not only to set an accessory view to the NSAlert but to set a "suppression checkbox". - DanGrover

