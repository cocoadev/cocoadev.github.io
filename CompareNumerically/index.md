---
layout: page
title: CompareNumerically
---

Ever wanted a comparison that put Cat2 before Cat20?

Here's chunk of code to allow one to CompareNumerically, as taken from a post on the CocoaDev mailing list:

----The original method [see also http://cocoa.mamasam.com/COCOADEV/2002/01/2/22178.php]----

    
 In general, the example I've posted yesterday deals with multiple embedded
 numbers as well. However, I just found one case where my implementation
 fails while SmartCompare works properly. Comparing aaa1bbb with aaa01ccc
 delivers 'equal' instead of 'ascending'.
 
 So here's a new, corrected version. It has been highly optimized (about 10
 times faster than the SmartCompare implementation). The performance may be
 relevant if you you use this method for sorting large string arrays.
 
 Best Regards, Norbert
 
 _____________________________________________
 Norbert Heger, Objective Development
 http://www.obdev.at/
 
 
 ---- NSString_ODCompareNumerically.h ---------------------------------------
 
 #import <Foundation/Foundation.h>
 
 @interface NSString (ODCompareNumerically)
 - (NSComparisonResult)compareNumerically:(NSString *)aString;
 @end
 
 ---- NSString_ODCompareNumerically.m ---------------------------------------
 
 #import "NSString_ODCompareNumerically.h"
 
 #define SELECTOR @selector(characterAtIndex:)
 typedef unichar(* unichar_IMP)(id,SEL,...);
 static inline BOOL isDecimalDigit(unichar c) { return c >= '0' && c <= '9';
 }
 
 static inline NSComparisonResult compareUnsigned (unsigned a, unsigned b) {
   return (a == b) ? NSOrderedSame
   : (a < b ? NSOrderedAscending : NSOrderedDescending);
 }
 
 static NSComparisonResult compareNumerically (NSString *s1, NSString *s2,
                                                   unsigned int offset1, unsigned int offset2,
                                                   unichar_IMP imp1, unichar_IMP imp2)
 {
   unsigned int len1 = [s1 length] - offset1;
   unsigned int len2 = [s2 length] - offset2;
   unichar c1 = 0, c2 = 0;
   
   // skip all common, nonnumeric characters
   unsigned int i, count = MIN(len1, len2);
   for (i = 0; i < count; i++) {
     c1 = imp1(s1, SELECTOR, offset1 + i);
     c2 = imp2(s2, SELECTOR, offset2 + i);
     if (c1 != c2 || isDecimalDigit(c1) || isDecimalDigit(c2)) { break; }
   }
   
   // one string is prefix of the other string
   // so we just need to compare the two lengths
   if (i == count) { return compareUnsigned(len1, len2); }
   
   else {
     BOOL isDecimal1 = isDecimalDigit(c1);
     BOOL isDecimal2 = isDecimalDigit(c2);
     
     // both substrings have a numeric prefix
     if (isDecimal1 && isDecimal2) {
       unsigned long long v1 = c1 - '0';
       unsigned long long v2 = c2 - '0';
       unsigned int i1, i2;
       
       // calculate the integer values
       for (i1 = i + 1; i1 < len1; i1++) {
         unichar c = imp1(s1, SELECTOR, offset1 + i1);
         if (isDecimalDigit(c)) { v1 = (10 * v1) + c - '0'; }
         else { break; }
       }
       for (i2 = i + 1; i2 < len2; i2++) {
         unichar c = imp2(s2, SELECTOR, offset2 + i2);
         if (isDecimalDigit(c)) { v2 = (10 * v2) + c - '0'; }
         else { break; }
       }
       
       // recursive function call if both values are equal
       return (v1 == v2)
       ? compareNumerically(s1,s2,offset1+i1,offset2+i2,imp1,imp2)
       : ((v1 < v2) ? NSOrderedAscending : NSOrderedDescending);
       
       // both characters are nonnumeric and not equal
     } else { return compareUnsigned(c1, c2); }
   }
 }
 
 @implementation NSString (ODCompareNumerically)
 
 - (NSComparisonResult)compareNumerically:(NSString *)aString
 {
   if (self == aString) return NSOrderedSame;
   else return compareNumerically(self, aString, 0, 0,
                                  (unichar_IMP)[self methodForSelector:SELECTOR],
                                  (unichar_IMP)[aString methodForSelector:SELECTOR]);
 }
 
 @end

