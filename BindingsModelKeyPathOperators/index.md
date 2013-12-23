---
layout: page
title: BindingsModelKeyPathOperators
---

You can bind a computed value from a NSArrayController using a few built in operators in the Model Key Path. For instance, if you have an NSArrayController of objects, each of which has a "pages" property. You can display the total number of pages setting:
Controller Key: arrangedObjects
Model Key Path: @sum.pages

there are a few others too:
@avg to average the values
@max to get the max value

and just putting @count will give you the total number of arranged items.

----

Here they are... look in KVC documentation for more info

*@avg 
*@count  
*@distinctUnionOfArrays  
*@distinctUnionOfObjects  
*@max  
*@min  
*@sum  
*@unionOfArrays  
*@unionOfObjects  


And here is a link to the documenation: http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html

----

Please correct me if the following isn't true:

Note that it seems like it is not possible to combine multiple array operators in the same expression like this:
Controller Key: arrangedObjects
Model Key Path: @unionOfArrays.bills.@sum.cost

In that case you have to either make a seperate ArrayController bounds to arrangedObjects (of the first ArrayController), @unionOfArrays.bills. And then use @sum.cost in the binding of for example a NSTextField (none-editable).

Or you can combine the expressions in code like this:

    
[[projectsController valueForKeyPath:@"selection.@unionOfArrays.bills"] valueForKeyPath:@"@sum.cost"]

