---
layout: page
title: NSMenuCategory
---



See also NSMenuItemCategory. -- DustinVoss
----
Header file for NSMenuCategory+CocoaDevUsersAdditions:
    
 #import <AppKit/NSMenu.h>
 
 @interface NSMenu (CocoaDevUsersAdditions)
 - (NSMenuItem *)nestedItemWithTag:(int)aTag;
 - (void)setSelectedItem:(NSMenuItem *)aMenuItem;
 @end


Implementation file for NSMenuCategory+CocoaDevUsersAdditions:
    
 #import "NSMenuCategory+CocoaDevUsersAdditions.h"
 
 @implementation NSMenu (CocoaDevUsersAdditions)
 
 - (NSMenuItem *)nestedItemWithTag:(int)aTag
 {
   int i;
   NSMenuItem* found = nil;
   NSMenuItem* item = nil;
   
   for (i = 0; i < [self numberOfItems]; i++)
   {
     item = [self itemAtIndex:i];
     if ([item hasSubmenu])
     {
       found = item submenu] nestedItemWithTag:aTag];
       if (found != nil)	break;
     }
     else if ([item tag] == aTag)
     {
       found = item;
       break;
     }
   }
   
   return found;
 }
 
 // Added 20 August 04. Sets aMenuItem to NSOnState (checked),
 // and turns the rest of the items off.
 -(void)setSelectedItem:(NSMenuItem *)aMenuItem
 {
   NSArray *myItems = [self itemArray];
   NSEnumerator *itemEnumerator = [myItems objectEnumerator];
   NSMenuItem *eachItem = nil;
   
   while (eachItem = [itemEnumerator nextObject])
   {
     [eachItem setState:([eachItem isEqual:aMenuItem] ? NSOnState : NSOffState)];
     // isEqual: is not guaranteed to return [[NSOnState if it's true
   }
 }
 
 @end


The NSMenuItem protocol is now deprecated, edited all id <NSMenuItem> to NSMenuItem*.


Category:CocoaDevUsersAdditions

