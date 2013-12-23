---
layout: page
title: ReadingRTFUsingTextView
---

Is there any method to convert NSData from RTF data to NSString?
If there is some text (with or without images) displayed in textView, then I can read it that way:     [NSString stringWithString:[textView string]], but I need to get NSString from a variable containing NSData.

----

You can create a NSAttributedString with the

     - (id)initWithRTF:(NSData *)rtfData documentAttributes:(NSDictionary **)docAttributes

method, and take the NSString from there.

    
// Assumes 'rtfData' is a valid NSData object containing valid RTF data.

NSAttributedString * attrString = [[NSAttributedString alloc] initWithRTF:rtfData documentAttributes:nil];
NSString * finalString = [attrString string];



I needed NSString for performing search with such methods as     rangeOfString:. Is it safe to search the resulting NSString; documentation for NSAttributedString  reads: "This method doesn�t strip out attachment characters; use NSText�s string method to extract just the linguistically significant characters."

There are methods for getting NSData from RTFD stream (such as     RTFDFromRange:), but is there any way to create an RTFD data from an empty string?

----

Simply create an NSAttributedString with an empty string (initWithString:@"") and then call that method using NSRange(0, 0)

Or just type this into your code:
    
{\rtf1\mac\ansicpg10000\cocoartf102
{\fonttbl}
{\colortbl;\red255\green255\blue255;}
\margl1440\margr1440\vieww9000\viewh9000\viewkind0
}


----

I'm having a serious problem getting my program to read RTF files. I'm following the tutorial located at 

http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/Tasks/ImplementingDocApp.html

My document subclass looks like this:
    
- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    // Add any code here that needs to be executed once the windowController has loaded the document's window.
	[textView setAllowsUndo:YES];
    if (fileContents != nil) {
        [textView setString:fileContents];
        [fileContents release];
		fileContents = nil;
    }
}

//------------------------------------------------------------------

- (NSData *)dataRepresentationOfType:(NSString *)aType 
{
    NSAssert([aType isEqualToString:@"rtf"], @"Unknown type");
    return [textView RTFFromRange:NSMakeRange(0, textView textStorage] length])];
}

// --------------------------------------------------------------------

- (BOOL)loadDataRepresentation:([[NSData *)data ofType:(NSString *)aType 
{
	NSLog(@"Test", nil);
    NSAssert([aType isEqualToString:@"rtf"], @"Unknown type");
    fileContents = [data copyWithZone:[self zone]];
	
	return YES;
	
}


Saving works fine; Any file I make will open fine with TextEdit. However, when I try to open a file using my program, it just opens a new window with the title of the document, but the textView in the window is empty. The Run window says:

    
2004-05-01 19:56:54.458 Document[2582] *** -[NSPageData _isCString]: selector not recognized
2004-05-01 19:56:54.466 Document[2582] *** -[NSPageData _isCString]: selector not recognized


-- MattBall

----

You're trying to send nsdata to a method that takes nsstring- try using:

    [textView replaceCharactersInRange:NSMakeRange(0,0) withRTFD:fileContents];

instead, or something similar to that.

