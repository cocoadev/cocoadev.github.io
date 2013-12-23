---
layout: page
title: UsingNSMenuItemEnableBinding
---

Having a (working) General/NSArrayController that feeds an General/NSTableView, I'd like to have a menu item, that is enabled only when something is selected in that General/NSTableView / General/NSArrayController.

I fire up IB and chose the General/NSMenuItem. Under bindings I choose 'enabled':

* Bind to: my General/NSArrayController
* Controller Key: selectedObjects
* Model Key Path: I have no idea, what model key I should use for this one
* Value Transformer (General/NSValueTransformer): Has to be General/NSIsNotNill


Doesn't work. Mainly because I have no idea, what the **Model Key Path** should be.

/Daniel

----

Try This:


* Bind to: my General/NSArrayController
* Controller Key: selectionIndexes
* Model Key Path: count
* Value Transformer (General/NSValueTransformer): Nothing (blank)


This works because the selectionIndexes is an array and     count returns the number of selected items in that array; selectedObjects should work also because it is an array, but this might be a little faster (?). If the count is zero then "enabled" will be set to false (zero). That is why you don't need a value transformer. Still, unless it is a simple application, you may want to handle all the menu activations through the code using the     validateMenuItem: method. Hope that helps.

-- General/RyanBates

----

Yes, I think I�ll use      validateMenuItem: mostly because I can�t have this binding to work:


* Bind to: myNSArrayController
* Controller Key: canSelectPrevious
* Model Key Path: Nothing (blank)
* Value Transformer (General/NSValueTransformer): Nothing (blank)


This works perfectly for the enabled binding of my Go Back button, but not for the Go Back menu!!

Actually, I can�t have any menu enabled binding to work! I also tryed with some BOOL keys in my model.

Maybe there are some settings to do? Should I toggle autoenable menu items or what??

--Flofl

----

Yes - on Panther it's definitely the case that you'll need to turn off autoenable menu items behaviour for the menu...
and yes - using myController.selection.@count or something similar is a good way of doing this... 

Ron
