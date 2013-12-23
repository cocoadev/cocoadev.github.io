---
layout: page
title: NSArrayControllerAndNSDictionary
---

How do I bind the keys in an NSDictionary to an NSArrayController? Should I create a separate NSArray that the controller can bind to? That seems rather redundant, so I am am wondering if I can do it differently.

Basically what I want to do is to populate an NSPopupButton with the keys of the dictionary - this all works fine using:

    [myPopup addItemsWithTitles: [myDict allKeys]];

Then, based on the users selection, the corresponding value of the key is used for some operation. Using target/action it is all working, but I would like to start using CocoaBindings instead. Any suggestions how I can achieve this?

- Koen.

----

I don't think this is hard.

The keys of a dictionary are held in an NSArray (returned via allKeys).  So all you need to do is set the keyPath for the NSArrayController's contentArray to this ( myDictionary.allKeys ).  Since it is KVC-compliant you will be able to access the array in this way and it should then be manageable from the Array Controller.  My guess is that you would have to set it to be uneditable and you won't just be able to insert / remove etc for obvious reasons.

HTH - JKP

----

Thanks - that worked, at least I don't get a runtime error anymore :) Now that I am using bindings, I think I don't need to use the popup IBOutlet to populate it (see code above). Rather I think that the bindings are supposed to do that now. But that part is not working yet. The selection of the menu seems to work, however, it does not display the correct titles, only a '0', and the popup is non-clickable.  I checked the ToDo's sample code by mmalc, but that uses two different arraycontrollers, which is not the case in my app. How do I make sure that the keys of the dictionary are displayed in the popup menu? 

- Koen.

----
I tried to help Koen too, and tested the use of myDictionary.allKeys as the key path. But it does not work. The array controller gets an empty array. In fact, the problem with a key path is that it might get confused with allKeys: should it be called on the collection, or on each element of the collection? The latter won't do anything good. The former would return an NSArray, but then it may consider it as one element, and not one collection. Whatever, something is fishy here.

I also checked the 'arrangedObjects' values of the array controller = empty array.

However, if instead of 'myDictionary.allKeys', I use 'myDictionaryKeys', and have this method in the bound object:

    
- (NSArray *)myDictionaryKeys
{
    return [myDicitionary allKeys];
}


everything works fine.

So Koen, if nobody comes up with something better, you could do that. -- CharlesParnot

----
That works great - thanks!

- Koen.

----

I have a followup question here. I have an array with dictionary entries in it, that I am using in an outlineview. That works fine. However, I am having problems getting the parent items to show up in the popupbutton. This is how my array looks like:

    
{
        CHILDREN = (
            {PARENT = <WeakReference: 0x3ef370>; TITLE = subitem1; }, 
            {PARENT = <WeakReference: 0x3ef380>; TITLE = subitem2; }
        ); 
        TITLE = item1; 
    }


So, in this example, the popupbutton should only show item1 (the only parent item here).
Now, I have tried to use the addItemsWithTitles: and my array directly, but it doesn't read it. Any suggestions?

- Vikingstad

