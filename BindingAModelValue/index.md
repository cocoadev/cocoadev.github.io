---
layout: page
title: BindingAModelValue
---

I have a configurable menu, where the menu item titles can be edited in a table view.

I let an array controller handle this table view, fetching the actual array from my model (so that the menu will be stored/retrieved from user defaults).

There is another controller which can create the actual menu, given an array of names. So basically what I want is to have this controller expose a value (e.g. MenuTitles) and bind this value to "arrangedObjects.Name" of the array controller.

So each time an item in the table view change, the binding will cause my menu building controller to receive a setMenuTitles: selector, with an array of the menu items as argument.

Unfortunately I have no idea how I should implement it in code.

I did figure out that I can add an observer to the array controller with a path of @"arrangedObjects.Name", but that requires me to implement code to deal with notifications, rather than just have it set the value.

----
Execute this statement:
    
[MenuController bind:@"MenuTitles" toObject:yourArrayController withKeyPath:@"arrangedObjects.Name" options:nil];

Then it should invoke setMenuTitles: on MenuController. For completeness, you may also wish to implement this class method in your MenuController:
    
+ (void)initialize
{
   [self exposeBinding:@"MenuTitles"];
}


----
I'm trying do to something very similar, I'm wondering if you'd be willing to share how you managed to get the array controller to populate itself with an array stored in user defaults.  I've tried various bindings on the contentArray of the array controller without any luck.  Thanks!

*I did not bind to user defaults, but File's Owner -- user defaults might be tricky with array controllers, because the array controller won't write the array back, or if it does, user defaults might not see it as a new array, and thus will not synchronize (a problem I often have when storing arrays in the user defaults, I always need to remove any existing array before setting a new one).*

