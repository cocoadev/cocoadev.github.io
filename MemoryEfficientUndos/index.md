---
layout: page
title: MemoryEfficientUndos
---



Here's a useful snippet from the CocoaDev mailing list -- KritTer

When you record an undo action, you record along with it the current value in question, whether it be a C int value or an Objective-C NSNumber object, for example, as far as I can tell. If your table's records (rows) are objects, their previous versions should therefore be held in the undo stack (by copying, not retaining, because the value is different, right? -- I think I read somewhere recently that they are copied). You want to watch out for memory issues if your records are large, but that's the price of multiple undo and redo.

In my application, it doesn't matter. I keep the "real" array of records in my model object, and a separate array in the table's data source object. I do this so that the latter can be filtered to display fewer than all of the records in the model array. So, when I "delete" a record, I just mark a "deleted" flag in the record and leave it in the model array for the time being, and filter it out of the data source array so it doesn't show in the table any longer. Undo and redo then easily get the "deleted" record back from the model array and filter it back into the data source array for display in the table, simply by resetting the "deleted" flag. My undo manager only has to preserve the setting of the flag, not the whole record object. I purge the model array of items that are marked "deleted" when the undo stack is cleared (e.g., when the document is saved).

Maintaining two separate arrays like this makes filtering really easy, and as a side effect it keeps deleted records around for undo and redo. It uses very little additional memory, because the records in each array share memory -- they are the same objects. The second array is just a bunch of pointers to objects in the first array. And this means that editing a record in one array automatically changes the value of the same record in the other array, with no additional code and no synchronization issues. I've been able to put this all together with just a handful of very simple methods.

Bill Cheeseman;
Quechee Software, Quechee, Vermont, USA;
http://www.quecheesoftware.com

