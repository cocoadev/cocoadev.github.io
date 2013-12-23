---
layout: page
title: NSCalendarDateCategory
---



**Source for NSCalendarDate+CocoaDevUsersAdditions.h :**
    
 #import <Foundation/Foundation.h>
 
 @interface NSCalendarDate (CocoaDevUsersAdditions)
 -(float)yearsSinceDate:(NSCalendarDate *)referenceDate;
 -(float)monthsSinceDate:(NSCalendarDate *)referenceDate;
 -(float)daysSinceDate:(NSCalendarDate *)referenceDate;
 -(float)hoursSinceDate:(NSCalendarDate *)referenceDate;
 -(float)minutesSinceDate:(NSCalendarDate *)referenceDate;
 -(float)secondsSinceDate:(NSCalendarDate *)referenceDate;
 @end


**Source for NSCalendarDate+CocoaDevUsersAdditions.m :**
    
 #import "NSCalendarDate+CocoaDevUsersAdditions.h"
 
 @implementation  NSCalendarDate (CocoaDevUsersAdditions)
 //note-- refactoring by JoeOsborn...  I wish there
 //were a better way to implement the first two.
 //um.. these are also untested.  Would someone please see if they work?
 
 -(float)yearsSinceDate:(NSCalendarDate *)referenceDate
 {
   return [self daysSinceDate:referenceDate]/365.2425; // Still not perfect, but way better than 365.
 }
 
 -(float)monthsSinceDate:(NSCalendarDate *)referenceDate
 {
   return [self yearsSinceDate:referenceDate]*12;
 }
 
 -(float)daysSinceDate:(NSCalendarDate *)referenceDate
 {
   return [self hoursSinceDate:referenceDate]/24;
 }
 
 -(float)hoursSinceDate:(NSCalendarDate *)referenceDate
 {
   return [self minutesSinceDate:referenceDate]/60;
 }
 
 -(float)minutesSinceDate:(NSCalendarDate *)referenceDate
 {
   return [self secondsSinceDate:referenceDate]/60;
 }
 
 -(float)secondsSinceDate:(NSCalendarDate *)referenceDate
 {
   int secs;
   [self years:NULL months:NULL days:NULL hours:NULL
       minutes:NULL seconds:&secs sinceDate:referenceDate];
   return (float)secs;
 }
 
 @end



Category:CocoaDevUsersAdditions

