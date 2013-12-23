---
layout: page
title: AppendingText
---

Unlike NSMutableString, NSTextView has not implemented a convenient method to append text to its existing text object.

You can append text to an NSTextView by creating a category on NSTextView and using code similar to that shown below:

    

@interface NSTextView(Controller)
- (void) appendString: (NSString *)str
@end

@implementation NSTextView(Controller)
-(void)appendString:(NSString *)str
{
    int len = self textStorage] length];
    [self replaceCharactersInRange:[[NSMakeRange(len,0)withString:str];
}


Invoke as usual, e.g., (a, b, and c are all declared integer variables)

    [reportView appendString:[NSString stringWithFormat:@"%i.%i.%i", a, b, c]];

