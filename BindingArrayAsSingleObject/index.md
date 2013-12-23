---
layout: page
title: BindingArrayAsSingleObject
---



Maybe I'm thinking about this too hard or approaching it the wrong way but i can't figure out how to solve this binding problem.

My data model is as follows with the entities: Items,Slots and Racks. (Think of wine bottles in a slot in a rack for example)

Items have many locations (more than one of that wine bottle type perhaps)
Each Slot is part of a rack (i.e. a rack contains slots)

So the model looks like:

item<--->>slot<<--->rack

Each entity has an attribute called name. And relations described above.

I have an NSScrollView bound to a 'slots' NSArrayController which lists the slots in the first column. In the second I want to show the rack that location in part of. I can get the column to give an editable drop down list of rack names to edit the slot object but I just want it to list the currently linked rack by name in an non-editable fashion. 

I have tried a number of ways of doing this but have not found one that works. 

Any help would be much appreciated. 

Cheers, Deano

----

Your NSTableColumn for "rack name" should be bound to the "slots" NSArrayController with Controller Key arrangedObjects and Model Key Path rack.name  (assuming "rack" is the name of the relationship in your Slot entity).  For making it non-editable, unchecking "Conditionally Sets Editable" for that binding should work. 
Note that table and outline bindings are mostly handled in NSTableColumn, certainly not in NSScrollView --PaulCollins

