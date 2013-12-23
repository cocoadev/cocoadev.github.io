---
layout: page
title: NSDictionaryValuesToTable
---

I wonder if anyone knows how to implement a NSDictionary in a NSTableView? I have only seen examples of loading a NSTableView with an NSArray, but none with a dictionary.

The example in this page:
http://cocoadevcentral.com/articles/000063.php

describes how to do this. I wonder if anyone could tell me how to do the same app but with an NSMutableDictionary instead of the Array they use.

----

Well, considering that an NSDictionary is inherently unordered and that a table view implies an order existing, this may not be the most elegant solution for whatever projection you're working on, but it does it.

Assuming you just want to display the values (and not the keys) in the table view, a simple command like the following:
    
NSArray *myArray = [myDictionary allValues];

will give you an array that contains all the values in your dictionary and you can use that as the data source for your table view as the tutorial you mentioned above did.

--KevinPerry

----

Hehe     [myDict objectForKey:[NSString stringWithFormat:@"%d", rowIndex]]

----

You could also sort the keys/values into some order that is meaningful to the table. Jon H.

----

Is the intention to fill *two* columns of the table view, one with key and one with value, sorted perhaps alphabetically by key?

