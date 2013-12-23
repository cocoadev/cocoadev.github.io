---
layout: page
title: NSPopUpButtonBindingsAndStaticContent
---

I have a Core Data based application I created for myself that has an NSPopUpButton populated via bindings.  The NSPopUpButton contains the names of all the courses I am taking this fall (the app is an assignment book).  I'd like to add have at the top of my listing an "Overview" item and then a separater item.  Below that would be the bound course listing from my data file.

mmalc has a description on his bindings homepage, but it doesn't seem to make much sense to me.  Has anyone done something similar and can offer me some more guidance?

Thanks.

- JustinWilliams

Taken from: http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

**Mixed static and dynamic content**

It is possible to have a popup button mixed with static and dynamic items using bindings. Assuming the content is managed by an NSArrayController, set the array controller's contentArray to be a variable provided by indexed accessor methods. In the indexed accessor methods you can "fake" the return values to return

You'll want to do something like this

    
-(int)countOfMenuItems
{
    // You want to add one extra item plus a separator so we need to add 2 "fake" items
    return ([dynamicItems count] + 2);
}

- objectInMenuItemsAtIndex:(unsigned int)index
{
    if (index == 0)
    {
        // return the overview item
    }
    else if(index == 1)
    {
        // return the separator
    }

    // Need to fudge the index into the dynamicItems array.  Since it's still indexed starting at 0 we subtract
    // however many "fake" items we said there were.
    return [dynamicItems objectAtIndex:(index - 2)];
}


This assumes that you're binding the popup to "MenuItems" and dynamicItems is bound to the stored courses.

- JimClause

