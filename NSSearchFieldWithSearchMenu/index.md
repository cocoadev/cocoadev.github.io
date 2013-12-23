---
layout: page
title: NSSearchFieldWithSearchMenu
---

Hi,

I have some very strange issues when using NSSearchField. I have attached an NSMenu to the SearchMenuTemplate outlet of the SearchField, but the menu ehibits some strange behaviour. The menu is grayed out even though it has attached and valid methods for each item, also, I have outlets in my controller to each of the menuItems in the NSMenu, BUT when I manipulate the menu items (changing titles, ticking/unticking items etc.) do not change in the NSSearchField menu.

Fantastically strangely I attach the NSMenu in question to another object to act as that objects menu, and the menu works perfectly here, I can use it as you would expect, disable enable things works fine, but still when viewed by clicking the magnifying glass in the NSSearchField the menu is static and has not been updated.

Is there something I have to call to update the NSSearchField menu. The final functionality I am looking for is like that in iTunes where one may select what they are searching for (artist, song name etc.).

Looked through the documentation but could not find any 'refresh' methods that might do the trick. This is very strange.

----

It works if you do it programatically. Sample code from Apple's docs:

    
- (void) awakeFromNib {
    NSMenu *cellMenu = [[[NSMenu alloc] initWithTitle:@"Search Menu"]
                        autorelease];
    NSMenuItem *item= [[NSMenuItem alloc] initWithTitle:@"Phone Numbers"
                                         action:@selector(limitSearch:)
                                         keyEquivalent:@""];
    id searchCell = [searchField cell];
    [cellMenu insertItem:item atIndex:0];
    [item release];
    [searchCell setSearchMenuTemplate:cellMenu];
}


----

Thanks, it seems that some coercing is necessary to get the menu to update properly when it's changed.

----

The trick is that setSearchMenuTemplate sets just a **template**. Therefore the searchfield might set up its own instances.
One way to update the state of a menu item is to use the sender instance of the action method because sender will be the corresponding menu item.

----

Similarly, you may find that after you've initially wired up an NSMenu to an NSSearchField in IB, new menu items will not enable. To enable them, try disconnecting and reconnecting the searchMenuTemplate outlet of the NSSearchField in IB.

