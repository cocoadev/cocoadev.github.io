---
layout: page
title: NSTextViewCategory
---



**NSTextView+CocoaDevUsersAdditions.h**
    
 @interface NSTextView (CocoaDevUsersAdditions)
 
 - (NSRange) visibleRange;
 //returns the range of the currently-visible text in the text view. 
 //Not extensively tested with non-wrapping text views,
 //but it seems to work okay (it returns the vertically visible range, 
 //including the line fragments that are 'chopped off' on the right)
 
 - (void) appendString: (NSString *)str;
 // How could they leave this one out??
 // See BookBuildingCocoaApplications p.341
 
 @end
 
 
 
 @implementation NSTextView (CocoaDevUsersAdditions)
 
 - (NSRange) visibleRange
 {
     NSRect visibleRect = [self visibleRect];
     NSLayoutManager *lm = [self layoutManager];
     NSTextContainer *tc = [self textContainer];
     
     NSRange glyphVisibleRange = [lm glyphRangeForBoundingRect:visibleRect inTextContainer:tc];;
     NSRange charVisibleRange = [lm characterRangeForGlyphRange:glyphVisibleRange  actualGlyphRange:nil];
     return charVisibleRange;
 }
 
 - (void) appendString: (NSString *) str
 {
     int len = self textStorage] length];
     [self replaceCharactersInRange:NSMakeRange(len, 0) withString:str];
 }
 
 @end


*Ummm, can't you just do     [[[self textStorage] mutableString] appendString:str] instead of the much more complex method     replaceCharactersInRange:withString:? (By complex I mean algorithmically) --[[JediKnil*

I rather doubt that it is more complex algorithmically. In fact, the two different methods will probably end up invoking the exact same core method to do the actual work.

*True, but JK's method is easier to grok. Someone make sure it works and change the code snippet, plz k thx! DustinVoss*


Category:CocoaDevUsersAdditions

