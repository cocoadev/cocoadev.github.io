---
layout: page
title: RemoveWhiteSpace
---

Here is some code that removes white space from a string, like new lines, tabs, and double spaces (not single spaces). It's intended as to be used in an NSString category. Example could be [@"   h\nello\r" removeWhiteSpace] would return @"hello"

Note: this is not to use as a trim function, for that use CFStringTrimWhitespace ((CFMutableStringRef) yourStringHere);

Enjoy!! --KevinWojniak

    - (NSString *)removeWhiteSpace
{
    NSMutableString *new = [NSMutableString string];
    NSArray *whites = [NSArray arrayWithObjects:@"\r", @"\n", @"  ", @"\t", nil];
    NSEnumerator *e = [whites objectEnumerator];
    id item;
    
    [new setString:self];

    while (item = [e nextObject]) {
        NSRange r = [new rangeOfString:item];
        
        while (r.location != NSNotFound) {
            [new replaceOccurrencesOfString:item withString:@"" options:nil range:NSMakeRange(0, [new length])];
            r = [new rangeOfString:item];
        }
    }
    
    return new;   
}

----

Sounds nifty.  Could be useful for someone trying to write an interpreter in Cocoa. --OwenAnderson

----
What you're doing is removing double spaces entirely - so if I wrote     "This is a  test."  I'd get back     "This is atest."
Since often when removing whitespace this isn't quite ideal, the following added in in place of the two-spacer works:
        
while([theString replaceOccurrencesOfString:@"  "
                                 withString:@" "
                                 options:NSLiteralSearch
                                 range:NSMakeRange(0, [theString length])]);

This will reduce all blank space down to a single space, which is often more what is wanted.  Hope this helps --DanKeen

----

Ah yes that was a *bug* - thanks for fixing it! --KevinWojniak

----
Another option for a trimming function is NSString's method stringByTrimmingCharactersInSet:  --SeanUnderwood
----
I'm pretty sure that stringByTrimmingCharactersInSet: is quite a bit slower than NSMutableString's replace occurrences method.  Anyone know for sure?

