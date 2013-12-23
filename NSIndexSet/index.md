---
layout: page
title: NSIndexSet
---

What is the easiest way to enumerate an index set?

I am currently stuck with code like this:
    
NSIndexSet* indices = ...;
unsigned int bufSize = [indices count];
unsigned int* buf = new unsigned int[bufSize];
NSRange range = NSMakeRange([indices firstIndex], [indices lastIndex]);
[indices getIndexes:buf maxCount:bufSize inIndexRange:&range];
for(unsigned int i = 0; i != bufSize; i++)
{
   unsigned int index = buf[i];
   ...
}

The only alternative I see is something like     firstIndex followed by     indexGreatorThan: � but that would most likely not give a linear running time.

----

From the docs:

    - (unsigned int)getIndexes:(unsigned int *)indexBuffer maxCount:(unsigned int)bufferSize inIndexRange:(NSRangePointer)range

*Use this method to quickly and efficiently traverse an index set. You usually use the return value as the bounds of a loop. In each iteration of the loop, you test for a particular case, using the current iteration as a reference to an index in the index set.*

So it looks like you're doing it the recommended way.

----

Is there an inherent advantage to NSIndexSets over NSEnumerators? In other words, why has the selectedRowEnumerator method in NSTableView been deprecated in favor of the seemingly more cumbersome selectedRowIndexes method? Thanks!

*I think NSIndexSet was introduced to allow using index sets in bindings, user defaults and scripts.  They then probably wanted to use them exclusively and made the older methods deprecated.  So we get more flexibility, but at a cost.  Although someone at the cocoa-dev mailing list said that indexGreaterThan: does cache the node of the last returned value, and thus, if one passes the last value, it will return the next index in O(1) time.  This makes it slightly easier to iterate an index set (efficiently).*

----
I just filed a report for getIndexes:maxCount:inIndexRange: regarding the inIndexRange: argument.  From experimenting it seems that you should only pass [indices lastIndex] as the length argument for getIndexes:maxCount:inIndexRange:.  The reason is the length of the range is used as the maximum index to return, not the maximum number of indices to return, hence the maxCount argument.  Also if you just want to get all of the indices in the set you can pass nil for the range argument.

----
To make it work correctly you have to change the fourth line into:
    
NSRange range = NSMakeRange([indices firstIndex], [indices lastIndex] + 1 );

The second argument of NSMakeRange is the length and not the endIndex.

----

So should it not be: 
    
NSRange range = NSMakeRange([indices firstIndex], ([indices lastIndex]-[indices firstIndex]) + 1);


----

Then why, does the following code seem to behave very erratically?  It only seems to work when I have the first item selected, and then it only works for the first element.  I have an NSTableView that contains a list of file items that I want to open.  

According to the docs, *A return value that is less than bufferSize indicates that you�ve reached the end of valid data in the index set*.  What I'm finding is that if I don't select the first element in the NSTableView, this call ALWAYS returns zero, and when I do have the first element selected, it only ever finds the first element (ie. the loop only occurs once), and none of the other selected items (the size variable always shows the correct number of items selected)

    
int i;
NSIndexSet *selected = [theTableView selectedRowIndexes];
int size = [selected count];

unsigned int arrayElement;
NSRange e = NSMakeRange(0, size);
    
while ([selected getIndexes:&arrayElement maxCount:1 inIndexRange:&e] > 0)
{
    // Do something really worthwhile with 'arrayElement'
}


I know that this is not the most efficient way of traversing the list, but I'm curious as to why this is failing...

*It fails because you start your size variable out as [selected count], but you should start it at [selected lastIndex] (or maybe [selected lastIndex] + 1).*

----

I like to interate through like this:
    
    unsigned current_index = [SOME_INDEX_SET firstIndex];
    while (current_index != NSNotFound)
    {
        //DO SOMETHING WITH INDEX current_index
        current_index = [SOME_INDEX_SET indexGreaterThanIndex: current_index];
    }


It's just cleaner.

*The original reason to avoid that is that indexGreaterThanIndex: needs to do a search -- but as stated above, someone at the CocoaDevML said it would internally cache the last query (but this is not documented).*

----

Hi all,

  I made a little class to help me around some of my confusions regarding NSIndexSet.  This class allows you to enumerate through an NSIndexSet using the Apple-suggested method of     (unsigned int)getIndexes:(unsigned int *)indexBuffer maxCount:(unsigned int)bufferSize inIndexRange:(NSRangePointer)range using simple enumerator-like messages.

Currently it doesn't support buffering (ie. if you have an IndexSet with one million entries, you'll end up with an internal array of one million entries), but it seems to work ok for what it is.  

  I loved the CocoaSTL version, however having to make my source files ObjC++ made the compilation a lot longer. 

  A basic code sample :-

    
    unsigned int current_index;
    NSIndexSet *selectedItems = [theView selectedRowIndexes];
    NSIndexSetEnumerator *e = [NSIndexSetEnumerator enumeratorWithIndexSet: selectedItems];
   
    while ([e nextIndex:&current_index])
    {
        // do something extremely exciting with 'current_index'
    }


Please note that this is NOT rigorously tested or supported code, I just thought I'd share something that made my life a little easier (note also the extreme lack of code comments :-) ) but the code is relatively self explanatory (I hope!).

You can download the source files at http://www.geocities.com/dagronf/code/

Darren.


**NSIndexSetEnumerator header file**
    
//
//  NSIndexSetEnumerator.h
//

#import <Foundation/Foundation.h>


@interface NSIndexSetEnumerator : NSObject 
{
    unsigned int m_uCurrentPosition;

    unsigned int m_uIndexCount;
    unsigned int *m_puIndexes;

}

// Basic initializers and deallocator

+ (id) enumeratorWithIndexSet:(NSIndexSet *)theSet;
+ (id) enumeratorWithIndexSet:(NSIndexSet *)theSet range:(NSRange)myRange;
- (id) initWithIndexSet:(NSIndexSet *)theSet range:(NSRange)myRange;
- (void) dealloc;

- (void) reset;
- (unsigned int) count;
- (BOOL) nextIndex:(unsigned int *)pValue;
- (unsigned int) indexAtIndex:(unsigned int)i;

@end



**NSIndexSetEnumerator source file**
    
//
//  NSIndexSetEnumerator.m
//

#import "NSIndexSetEnumerator.h"


@implementation NSIndexSetEnumerator

+ (id) enumeratorWithIndexSet:(NSIndexSet *)theSet
{
    NSRange myRange = NSMakeRange([theSet firstIndex], [theSet lastIndex] + 1);
    return [NSIndexSetEnumerator enumeratorWithIndexSet:theSet range:myRange];
}

+ (id) enumeratorWithIndexSet:(NSIndexSet *)theSet range:(NSRange)myRange
{
    return [[[NSIndexSetEnumerator alloc] initWithIndexSet:theSet range:myRange] autorelease];
}

- (id) initWithIndexSet:(NSIndexSet *)theSet range:(NSRange)myRange
{
    self = [super init];
    if (!self) 
    {
        return nil;
    }

    m_uCurrentPosition = -1;
    
    m_uIndexCount = [theSet count];
    m_puIndexes = (unsigned int *)malloc(sizeof(unsigned int) * m_uIndexCount);

    [theSet getIndexes:m_puIndexes maxCount:m_uIndexCount inIndexRange:&myRange];
   
    return self;
}

-(void) dealloc
{
    free(m_puIndexes);
    [super dealloc];
}

-(BOOL)nextIndex:(unsigned int *)pValue
{
    m_uCurrentPosition++;
    if (m_uCurrentPosition >= m_uIndexCount)
        return FALSE;
    *pValue = m_puIndexes[m_uCurrentPosition];
    return TRUE;
}

- (unsigned int) indexAtIndex:(unsigned int)i
{
    if (i >= m_uIndexCount)
        return NSNotFound;
    return m_puIndexes[i];
}

-(void)reset
{
    m_uCurrentPosition = -1;
}

-(unsigned int)count
{
    return m_uIndexCount;
}

@end



*You should not be creating new methods like +allocWith.... Follow Apple's conventions; "alloc" is used to create an object but not initialize it. Convenience methods that return autoreleased objects get named with part of the class name, in this case, something like +enumeratorWithSet:*

**It's a wiki -- you can change stuff like this :) Changed.**

----

Anyone know how to appropriately iterate through a set in reverse order? -Seb

*By definition, a set has no order. Of course, an index set does (the order of the indices themselves), but I guess that is not guaranteed. Perhaps a future implementation of NSIndexSet will store table rows in the order they are clicked on, rather than top to bottom. --JediKnil*

I could be wrong, but I'm guessing he wants to know how to iterate from largest to smallest, rather than the traditional smallest to largest.

*Well, you could try this:*     for(unsigned int i = bufSize - 1; i >= 0; i--)

**I don't think that will work, as the indexes are copied in smallest to largest anyway. If your buffer is smaller than the number of indexes, you may get output like this: 3, 2, 1, 5, 4. Of course, if you check the size of the set and     malloc/    realloc a new buffer each time it's too small, you could fix the problem...for now. Of course, this totally defeats NSIndexSet's lightweight structure of using ranges to avoid storing every single index. --JediKnil**

I'm new at Cocoa, but this variation of the code above worked in my case  --Roger
    	NSIndexSet *rows;
	rows = [tableView selectedRowIndexes];
	
	unsigned current_index = [rows lastIndex];
    while (current_index != NSNotFound)
    {
	// do something with the index
        current_index = [rows indexLessThanIndex: current_index];
    }


----

I believe some of Apple's documentation/examples regarding the "getIndexes:maxCount:inIndexRange:" method is incorrect. If you pass a NSRange pointer for the "inIndexRange" argument it:

1) sets the "location" of the range to be the the next *integer* after the last index added to the buffer not *necessarily* the next index in the set (i.e. if 60 is the last index added to the buffer then 61 is the new location of the range, whether or not 61 is actually an index in that set)

2) sets the "length" of the range to be such that NSMaxRange() is the same before and after (i.e. the new length is old_location+old_length-new_location)

Thus under the description in the NSIndexSet Class Reference I believe there is an error: 

"and sets indexRange to (20, 80)." should be "and sets indexRange to (21, 80)." since it actually sets the range to the first index that was not included in the buffer and 20 is included.

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexSet_Class/Reference/Reference.html#//apple_ref/occ/instm/NSIndexSet/getIndexes:maxCount:inIndexRange:

Additionally, there is a similar error in the "NSIndexSet.h" header:

"and the range would be modified to (40, 60)." should actually be "and the range would be modified to (39, 61)." because the last index that was included was 38 and the next is 39. Even though 39 is not in the set it appears to set the range location to the next integer, not necessarily the next index in the set.

-Tom
----

Here's my little version of an enumerator for NSIndexSet. Hasn't been tested very much but seems to work for my cases. --KevinWojniak
    
@interface IndexSetEnumerator : NSObject {
    NSIndexSet *set;
    unsigned int idx;
    BOOL first;
}
- (id)initWithIndexSet:(NSIndexSet *)indexSet;
- (unsigned int)nextIndex;
@end
@implementation IndexSetEnumerator
- (id)initWithIndexSet:(NSIndexSet *)indexSet {
    if ([super init]) {
        set = [indexSet retain];
        first = YES;
    }
    return self;
}
- (void)dealloc {
    [set release];
    [super dealloc];
}
- (unsigned int)nextIndex {
    if (first) {
        idx = [set firstIndex];
        first = NO;
    }
    else
        idx = [set indexGreaterThanIndex:idx];
    return idx;
}
@end

@interface NSIndexSet (IndexSetEnumerator)
- (IndexSetEnumerator *)indexEnumerator;
@end
@implementation NSIndexSet (IndexSetEnumerator)
- (IndexSetEnumerator *)indexEnumerator {
    return [[[IndexSetEnumerator alloc] initWithIndexSet:self] autorelease];
}
@end


An example:
    
IndexSetEnumerator *setEnum = [myIndexSet indexEnumerator];
NSUInteger idx = NSNotFound;
while ((idx = [setEnum nextIndex]) != NSNotFound)
    printf("index: %d\n", idx);


----

Found myself needing to convert back and forth between an index set and a text representation - a comma-separated series of individual indices and ranges. Here's what I came up with:

NSIndexSet_StringRepresentation.h
    
#import <Cocoa/Cocoa.h>

@interface NSIndexSet (StringRepresentation)
+ (id)indexSetWithStringRepresentation:(NSString*)inString;
- (id)initWithStringRepresentation:(NSString*)inString;
- (NSString*)stringRepresentation;
@end

@interface NSMutableIndexSet (StringRepresentation)
- (void)addIndexesFromStringRepresentation:(NSString*)inString;
- (void)removeIndexesFromStringRepresentation:(NSString*)inString;
@end


NSIndexSet_StringRepresentation.m
    
#import "NSIndexSet_StringRepresentation.h"

static NSString* LocalizedListSeparator(void)
{
  NSString* theResult = nil;
  CFLocaleRef theLocale = CFLocaleCopyCurrent();
  if(theLocale)
  {
    CFTypeRef theValue = CFLocaleGetValue(theLocale, kCFLocaleGroupingSeparator);
    if(theValue && (CFGetTypeID(theValue) == CFStringGetTypeID()))
    {
      theResult = [NSString stringWithFormat:@"%@ ", (NSString*)theValue];
    }
    CFRelease(theLocale);
  }
  
  if(theResult == nil) theResult = [NSString stringWithString:@", "];
  
  return theResult;
}

@implementation NSIndexSet (StringRepresentation)

+ (id)indexSetWithStringRepresentation:(NSString*)inString
{
  return [self alloc] initWithStringRepresentation:inString] autorelease];
}

- (id)initWithStringRepresentation:([[NSString*)inString
{
  NSMutableIndexSet* theTempSet = [NSMutableIndexSet indexSet];
  NSArray* theChunks = [inString componentsSeparatedByString:LocalizedListSeparator()];
  NSEnumerator* theChunkEnumerator = [theChunks objectEnumerator];
  NSString* theChunk = nil;
  while((theChunk = [theChunkEnumerator nextObject]) != nil)
  {
    NSRange theDash = [theChunk rangeOfString:@"-"];
    if(theDash.location == NSNotFound) [theTempSet addIndex:[theChunk intValue]];
    else
    {
      unsigned theFirstIndex = theChunk substringToIndex:theDash.location] intValue];
      unsigned theLastIndex = [[theChunk substringFromIndex:theDash.location + 1] intValue];
      unsigned theCount = theLastIndex - theFirstIndex + 1;
      [theTempSet addIndexesInRange:[[NSMakeRange(theFirstIndex, theCount)];
    }
  }
  return [self initWithIndexSet:theTempSet];
}

- (NSString*)stringRepresentation
{
  NSMutableString* theResult = nil;
  NSRange theWorkRange = NSMakeRange([self firstIndex], 1);
  if(theWorkRange.location != NSNotFound)
  {
    NSString* theSeparator = LocalizedListSeparator();
    while(theWorkRange.location != NSNotFound)
    {
      unsigned theNextIndex = [self indexGreaterThanIndex:theWorkRange.location];
      while(theNextIndex == (theWorkRange.location + theWorkRange.length))
      {
        ++theWorkRange.length;
        theNextIndex = [self indexGreaterThanIndex:theNextIndex];
      }
      
      if(theResult) [theResult appendString:theSeparator];
      else theResult = [NSMutableString string];
      
      if(theWorkRange.length == 1)
      {
        [theResult appendFormat:@"%u", theWorkRange.location];
      }
      else
      {
        unsigned theFirstIndex = theWorkRange.location;
        unsigned theLastIndex = theFirstIndex + theWorkRange.length - 1;
        [theResult appendFormat:@"%u-%u", theFirstIndex, theLastIndex];
      }
      
      theWorkRange.location = theNextIndex;
      theWorkRange.length = 1;
    }
  }
  
  if(theResult == nil) theResult = [NSString string];
  
  return theResult;
}

@end

@implementation NSMutableIndexSet (StringRepresentation)

- (void)addIndexesFromStringRepresentation:(NSString*)inString
{
  [self addIndexes:[NSIndexSet indexSetWithStringRepresentation:inString]];
}

- (void)removeIndexesFromStringRepresentation:(NSString*)inString
{
  [self removeIndexes:[NSIndexSet indexSetWithStringRepresentation:inString]];
}

@end


----

Hi, there!

Just wanted to quickly share something with you. Last couple of days I've been searching for easy and efficient way to iterate over a huge     NSIndexSet, one that, let's say, represents parts of a file. I would expect to have some sort of     rangeAtIndex:, but I couldn't find anything resembling like it in Apple documentation. Well, it turns out there is just the thing. If you run     nm on Foundation library, it shows up as a     NSIndexSet instance method.

In the end I couldn't get it to work, so I set out to reverse engineer the function. This is the result. It'll probably break by the time 10.7 comes out, but hopefully by that time this will be fixed.

    
@interface RDIndexSet: NSIndexSet

- (NSUInteger) rangeCount;
- (NSRange) rangeAtIndex: (NSUInteger) index;

@end

@implementation RDIndexSet

- (NSUInteger) rangeCount {
	
	if ( _indexSetFlags._isEmpty )
		return 0;
	
	else if ( _indexSetFlags._hasSingleRange )
		return 1;
	
	else {
		
		NSUInteger * count = _internal._multipleRanges._data + sizeof(NSUInteger);
		return *count;
	}
	
}
- (NSRange) rangeAtIndex: (NSUInteger) index {
	
	NSRange * range;
	
	if ( _indexSetFlags._isEmpty || index >= [ self rangeCount ] )
		return NSMakeRange(0, 0);
	
	else if ( _indexSetFlags._hasSingleRange ) {
		
		range = (NSRange *)&_internal._singleRange;
		return *range;
		
	} else {
		
		range = (NSRange *)(_internal._multipleRanges._data + index * sizeof(NSRange) + 7 * sizeof(NSUInteger) );
		return *range;
	}
	
}

@end


Oh, and maybe someone with Tiger installation can test it out. Cheers!

