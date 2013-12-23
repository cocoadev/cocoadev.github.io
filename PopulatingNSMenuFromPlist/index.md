---
layout: page
title: PopulatingNSMenuFromPlist
---



How do I do this? I'm familiar with addItemAtIndex, but I'm not all that sure how to automatically add every item in my bookmarks plist. Also, how would I save all the menu items and their representedObjects in my menu into a plist?




Thanks, and thanks again everyone for being here. I've learned more here than I could have from a whole pile of Cocoa books.

---- 

Menu content should not be used as data model, hence exporting to plist is useless.

I'm describing a way to populate a hierarchical menu, with values or key/value pairs.

First, create a plist file containing a hierarchy of arrays, one array per menu. I'm using arrays since ordering of items is preserved, hence positions in the menus.

    
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<array>
  <string>ncbi databases</string>
  <array>
    <string>Human genome related databases</string>
    <string>Ab nitio (predicted) mRNA sequences~hs_genome/gnomon_rna</string>
    <string>Ab nitio (predicted) protein sequences~hs_genome/gnomon_prot</string>
    <string>Build RNA~hs_genome/protein</string>
  </array>
  <array>
    <string>Specialized Rat Sequence databases</string>
    <string>latest public rat genome build~rn_genome/genome</string>
    <string>mRNAs predicted off the genome build~rn_genome/gnomon_rna</string>
  </array>
</array>
</plist>


Then a factory will create a menu from the plist file.

    
//
//  MenuFactory.h
//
//  Created by Colas Schretter on 03/08/05.
//  Copyright 2005. All rights reserved.
//
//  Create and feed a menu containing key/value pairs.
//  Read a hierarchy of array from a property file.

#import <Cocoa/Cocoa.h>

@interface MenuFactory : NSObject {}
+ (NSMenu*)menuWithPropertyListFile:(NSString *)fileName;
+ (NSMenu*)menuWithArray:(NSArray *)array;
@end

//
//  MenuFactory.m
//
//  Created by Colas Schretter on 03/08/05.
//  Copyright 2005. All rights reserved.
//

#import "MenuFactory.h"

@implementation MenuFactory

+ (NSMenu*)menuWithPropertyListFile:(NSString *)fileName {
	// read a hierarchy of key/value pairs on file
	NSMenu *menu = [MenuFactory menuWithArray:[NSArray arrayWithContentsOfFile:fileName]];
	
	// we insert a separator between lists and recent choices
	[menu addItem:[NSMenuItem separatorItem]];
	
	return menu;
}

// feed the menu with array items
+ (NSMenu*)menuWithArray:(NSArray *)array {
	NSMenu *menu = [[NSMenu new] autorelease];

	// the first entry of an array is its title
	int i;
	for (i = 1; i < [array count]; ++i) {
		if(array objectAtIndex:i] isKindOfClass:[[[NSArray class]] ) {
			NSMenuItem *item = [[NSMenuItem new] autorelease];
			NSMenu *submenu = [MenuFactory menuWithArray:[array objectAtIndex:i]];

			// add the item to the menu
			[menu addItem:item];

			// attach the submenu to the current item
			[menu setSubmenu:submenu forItem:item];

			// the first entry of an array is its title
			NSString *title = array objectAtIndex:i] objectAtIndex:0];
			[[NSAttributedString *attributedTitle = [[[NSAttributedString alloc] initWithString:title] autorelease];
			[item setAttributedTitle:attributedTitle];
		} else if (array objectAtIndex:i] isEqualToString:@""]) {
			// we insert a separator instead of blank entries
			[menu addItem:[[[NSMenuItem separatorItem]];
		} else {
			NSMenuItem *item = [[NSMenuItem new] autorelease];

			// add the item to the menu
			[menu addItem:item];

			// cut our string in a possible key/value pair
			NSArray *tokens = array objectAtIndex:i] componentsSeparatedByString:@"~"];
			[[NSString *key = [tokens objectAtIndex:0];
			NSString *value = @"";
			if ([tokens count] > 1)
				value = [tokens objectAtIndex:1];

			// set the presentation title of the item
			NSAttributedString *attributedTitle = [[[NSAttributedString alloc] initWithString:key] autorelease];
			[item setAttributedTitle:attributedTitle];

			// set the hidden value (possibly empty)
			[item setTitle:value];
			
			// send action to first responder
			[item setTarget:nil]; 
			[item setAction:@selector(propertyMenuItemSelected:)];
		}
	}

	return menu;
}

@end

__________________________________________________________________________________________________________________________________

Hi, I tried the previous code in my project and it doesn't work ("initwithcoder" is not found ),the program needs to implement the NSCoding protocol.So I wrote this in MenuFactory.h :
     
//
//  MenuFactory.h
//
//  Created by Colas Schretter on 03/08/05.
//  Copyright 2005. All rights reserved.
//
//  Create and feed a menu containing key/value pairs.
//  Read a hierarchy of array from a property file.

#import <Cocoa/Cocoa.h>

@interface MenuFactory : NSObject <NSCoding> {} 
+ (NSMenu*)menuWithPropertyListFile:(NSString *)fileName;
+ (NSMenu*)menuWithArray:(NSArray *)array;
- (id)  initWithCoder:(NSCoder*) coder;
- (void) encodeWithCoder:(NSCoder*) coder;

@end


The problem is that I can't write anything inside "initWithCoder" definition without errors like  "-[MenuFactory supermenu]: selector not recognized [self = 0x33b0a0]"; so this is the code I added in Menufactory.m:
    
- (id)   initWithCoder:(NSCoder*) coder
{
	[super init];
	[coder decodeObjectForKey:@"array"];
	return self ;
}


- (void)  encodeWithCoder:(NSCoder*) coder { }


 

How can I use "MenuFactory"'s code without errors and with little modifications only? 
Angelo

----

I think you're confused. The purpose of the MenuFactory CLASS is to make pre-populated NSMenu objects from plists on demand. There is no instance of MenuFactory ever made or needed so there is no purpose in being able to archive or dearchive one. Making it adopt the NSCoding protocol is unnecessary and fruitless. The error you're seeing is because having done this, you've confused menuFactory with NSMenu itself - it has no 'supermenu', hence it's not found.

My advice is to undo your changes and understand what's going on. MenuFactory's job is to manufacture menus (that's what factories do!). As the original responder suggested, you should not be saving the contents of menus (i.e. doing so is attempting to use the menu itself as a data model, when in fact all it is is a user interface representation (view)). A better idea is to have some internal representation of your data (model) and generate the menu from it, and to save/restore this model. You need that third step - you're trying to cut corners whether you realise it or not, and it's biting you.

One other very minor error to note in passing - if you make a class adopt a protocol like <NSCoding>, you don't need to declare the methods of the protocol as part of the @interface - it's already implied. You just need to implement the methods in your @implementation. However, it's harmless to do so, it just adds unnecessary clutter. Hope this helps. --GrahamCox

----

First,I'm not a Cocoa expert,so thank you for your help and for your patience.I'm writing an application that lets you do annotations on pdf documents and lets you save them as xml files (it's my university thesis).
I think could be useful to have, along with a "load recent pdfs" menu a "load recent annotations" menu.

So I need an Nsarchive containing NSUrls to files (I can do that) that every time I click on the menu,shows a submenu with a list of recent "annotations" files (loaded from a xml file) and when I click on one of them opens the files' url.
I think that could be the right move populating the menu with methods called inside "awakeFromNib" written in the same window controller i'm using for annotations loading/saving.

Could you please show me the direction for the solution? I'm confused.
Angelo
----

Think about it in terms of model-view-controller. Your model is a list of files. A good container for a list is an NSArray. So one possibility is to maintain an NSArray of NSURL objects. You can write methods that add or remove items from this list. You can archive the array to and from a plist (XML), since NSArray already supports the NSCoding protocol. So far so good. One view of this model is a menu, so you need a method to populate a menu from the list. Some code similar to the above MenuFactory would be a good starting point - just iterate over the list, extract part of the URL (you might not want to have the full pathname, just the last component - NSString has methods for breaking down paths) and make each menu item. You need to rebuild the menu whenever the list changes, not just at -awakeFromNib time. If you wrap your container in a model object of your own, this is very easy to arrange. Finally, what happens when the user selects an item in the menu? This is where the controller part comes in. It responds to the menu selection by looking up the relevant URL item, and performing the required action - opening the file say. In reality to do all this requires very little code - just a handful of very short methods in one or two new objects. It's a very well-defined problem space with 99% of the solution provided for you by Cocoa as is - you just have to join the right bits together really.

It's well worth getting a book on Cocoa programming aimed at beginners - the Hillegass one ("Cocoa Programming for Mac OS X") is very good for example. If you work through the book, which might only take a few days if you were enthusiastic about it, all this will become blindingly obvious. --GC

