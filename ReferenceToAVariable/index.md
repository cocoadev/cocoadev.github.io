---
layout: page
title: ReferenceToAVariable
---

I'm not even sure to call what I need help with. However, I will explain it. 

In my program I have a NSDictionary, one of the dicitonary keys is an array. In this array each object can be an id key or another array depending on the situation. I have a method that is called to when a new item is found and it contains the position of the object down the tree.. so i have a for loop that loops through each position but I need the current MutableArray that the loop is working with. I have figured out how I can do it, just not how it can be implemented. 

I figure if I have a variable called previousObject. before the loop starts I set the "previousObject" to the main array then as the loop ends I change "previousObject" to the item of the array that is being worked with at the index specified.

Is there any way I can make previousObject a reference to the array I'm working with, so if I send previousObject an addObject message then it will happen to the array previousObject points to? This seems to be the simplest solution to me and I was wondering if it was possible?

Thanks, it's much appreciated.

Tommy

----

Sure, just make previousObject be an NSMutableArray*. It's just an object pointer; you can copy those around with =. For example,     previousObject = someArray; [previousObject addObject:item]; will add     item to     someArray.

