---
layout: page
title: NSMenuItemCategory
---



This uses UndocumentedGoodness, so maybe don't rely on it too much. It sets the dynamic menu item attribute, which allows the menu to change depending on which keys are held down. For usage, see Menus.h -- DustinVoss

I think this has been properly exposed in the InterfaceBuilder now. You can set up consecutive menu items and set them as "alternates", based on the modifier keys. This causes the NSMenuItem underneath to change - meaning you can bind separate text, icons and actions to the alternates.  -- MikeSolomon

Correct. The new Panther APIs are **alternate** and **setAlternate**, making these obsolete.

If you want to do this in Jaguar or below without using the undocumented parts, you can subclass NSWindow for the main window of your app, and override the - (void)flagsChanged:(NSEvent *)theEvent method. In your method you can change the NSMenuItem which is to have an option key alternative using setTitle:, setAction and setKeyEquivalent:

This won't work if the user has already clicked on the menu though, will it? It seems to me that the application doesn't get the flagsChanged: message when the menu is showing...


----
Header file for NSMenuItemCategory+CocoaDevUsersAdditions:
    
 #import <AppKit/AppKit.h>
 
 @interface NSMenuItem (CocoaDevUsersAdditions)
 - (void)setDynamic:(BOOL)aFlag;
 - (BOOL)isDynamic;
 @end


Implementation file for NSMenuItemCategory+CocoaDevUsersAdditions:

    
 #import "NSMenuItemCategory+CocoaDevUsersAdditions.h"
 #import <Carbon/Carbon.h>
 
 @implementation NSMenuItem (NSMenuItemExtension)
 
 // Must be called after NSApp delegate's applicationDidFinishLaunching.
 - (void)setDynamic:(BOOL)aFlag
 {
   OSStatus err;
   // If the following ever becomes obsolete, can use Carbon's AcquireRootMenu and work my way to the item.
   extern MenuRef _NSGetCarbonMenu(NSMenu *);
   NSMenu *cocoaMenu = [self menu];
   int item = [cocoaMenu indexOfItemWithTag:[self tag]] + 1;
   MenuRef carbonMenu = _NSGetCarbonMenu (cocoaMenu);
   
   err = ChangeMenuItemAttributes (carbonMenu, item,
                                       (aFlag ? kMenuItemAttrDynamic : 0),
                                       (aFlag ? 0 : kMenuItemAttrDynamic) );
   NSAssert2 (!err, @"Couldn't set dynamic attribute of menu item %@ (err %ld).", [self title], err);
 }
 
 - (BOOL)isDynamic
 {
   OSStatus err;
   // If the following ever becomes obsolete, can use Carbon's AcquireRootMenu and work my way to the item.
   extern MenuRef _NSGetCarbonMenu(NSMenu *);
   NSMenu *cocoaMenu = [self menu];
   int item = [cocoaMenu indexOfItemWithTag:[self tag]] + 1;
   MenuRef carbonMenu = _NSGetCarbonMenu (cocoaMenu);
   MenuItemAttributes attr;
   
   err = GetMenuItemAttributes (carbonMenu, item, &attr);
   NSAssert2 (!err, @"Couldn't get attributes of menu item %@ (err %ld).", [self title], err);
   return (attr & kMenuItemAttrDynamic);
 }
 
 @end



Category:CocoaDevUsersAdditions

