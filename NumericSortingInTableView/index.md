---
layout: page
title: NumericSortingInTableView
---

I am trying to sort an NSMutableArray of NSStrings numerically. I tried using     compare: options:NSNumericSearch as a sort selector in Interface Builder, but it wouldn't let me.

I have an idea that I could make a convenience method for     -(NSComparisonResult)compare:(NSString *)*aString* options:(unsigned)NSNumericSearch called     -(NSComparisonResult)numericalCompare:(NSString *)*aString*, but I have no idea how to get Interface Builder to use this custom method as a sort selector. 

I have looked in the IB help, XCode docs, and several sites, but the information was very difficult to find. Can anyone help me?

----

Try to write a NSString category, and specify     numericalCompare: as a sort selector.

    
@interface NSString (My<nowiki/>Additions)
@end

@implementation NSString (M<nowiki/>yAdditions)

-(NSComparisonResult)numericalCompare:(NSString *)otherString {
  return [self compare:otherString options:NS<nowiki/>NumericSearch];
}

@end


-- DenisGryzlov

