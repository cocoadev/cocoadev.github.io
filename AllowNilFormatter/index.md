---
layout: page
title: AllowNilFormatter
---

For a database app I'm writing I need text fields to preserve NULL values.

In a normal text field, [NSNull null] as the value renders to the empty string, and tabbing through a field set to null will convert the field's value to the empty string. What we want is for the field to stay null if the value isn't changed, as some database columns may allow NULL as a value. I coded a solution by subclassing NSFormatter to preserve null values, and render them in a different color.

-- AdamVandenberg

----

**AllowNilFomatter.h**

    
 // This code is placed in the public domain - AdamVandeberg
 #import <Foundation/Foundation.h>
 
 @interface AllowNilFormatter : NSFormatter
 {
 /*
     Is the field value an actual [NSNull null]?
     
     Typing clears isHardNull, getObjectValue: for a null sets isHardNull.
     To set it while editing, some keypress needs to set the objectValue
     for the underlying Text control no [NSNull null].
 */
     BOOL isHardNull;
     NSString *nullString;
     [NSAttributedString *_attributedStringForNil;
 }
 
 - (NSAttributedString *) attributedStringForNil;
 - (void) setAttributedStringForNil: (NSAttributedString *) anAttributedStringForNil;
 
 @end


----

**AllowNilFomatter.m**

    
 // This code is placed in the public domain - AdamVandeberg
 #import "AllowNilFormatter.h"
 
 @implementation AllowNilFormatter
 - (id)init
 {
     if ((self=[super init]))
     {
         isHardNull = NO;
         nullString = @"<NULL>";
         
         _attributedStringForNil = 
            NSAttributedString alloc] initWithString: nullString
             attributes: [NSDictionary dictionaryWithObjectsAndKeys:
                 [NSColor redColor], NSForegroundColorAttributeName, nil;
     }
     return self;
 }
 
 - (void)dealloc
 {
     [_attributedStringForNil release];
     [super dealloc];
 }
 
 - (NSAttributedString *) attributedStringForNil 
 { return _attributedStringForNil retain] autorelease]; }
 
 - (void) setAttributedStringForNil: 
    (NSAttributedString *) anAttributedStringForNil {
     if (_attributedStringForNil != anAttributedStringForNil) {
         [_attributedStringForNil release];
         _attributedStringForNil = [anAttributedStringForNil copy];
     }
 }
 
 
 - (NSAttributedString *)attributedStringForObjectValue:(id)anObject 
    withDefaultAttributes:(NSDictionary *)attributes
 {
     if (anObject == [NSNull null])
     {
         isHardNull = YES;
         return _attributedStringForNil;
     }
     else
     {
         isHardNull = NO;
         return nil;	// Use normal string
     }
 }
 
 - (NSString *)stringForObjectValue:(id)anObject
 {
     if (anObject == [NSNull null])
     {
         isHardNull = YES;
         return nullString;
     }
     else
     {
         isHardNull = NO;
         return [anObject description];
     }
 }
 
 - (BOOL)getObjectValue:(id *)anObject forString:(NSString *)string 
    errorDescription:(NSString **)error
 {
     if (isHardNull)
         *anObject = [NSNull null];
     else
         *anObject = string;
 
     return YES;
 }
 
 - (BOOL)isPartialStringValid:(NSString **)partialStringPtr 
    proposedSelectedRange:(NSRangePointer)proposedSelRangePtr 
    originalString:(NSString *)origString 
    originalSelectedRange:(NSRange)origSelRange 
    errorDescription:(NSString **)error
 {
     // TODO: If isHardNull, should knock out <NULL> and replace with new text only.
     isHardNull = NO;
     return YES;
 }
 
 @end;



----

Note that this code only solves half of my problem, preserving and displaying null values in a field. The other half is setting null values in a field in the first place. I'd like to be able to hit ^0 (control-Zero)  in a field and get it set to [NSNull null], but I can't quite figure that out. I may have to dive into customizing the [[FieldEditor used for those cells, but I'd rather not. I did a partial solution that worked OK for text fields, but didn't work so well for cells in a table view (editing a cell and then pressing the key to change the value to null did that, except that the cell then displayed in white-on-white text.)

If anyone has an example of getting a text field / cell to respond to a custom control-key hot key, I'd love to see it.

-- AdamVandenberg

