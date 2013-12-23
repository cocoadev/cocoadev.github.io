---
layout: page
title: ManualBindingsAndNilPlaceholders
---



I'm creating my UI in code (for "security through obscurity" as well as educational purposes). I have a table with a column that has an NSPopUpButtonCell as its data cell. The popup cell's content, contentObjects, and contentValues bindings are all bound to a controller that controls a list of "status" items. The list pops up and works just fine. The table column's selectedObject is bound to my table's array controller via arrangedObjects.status. Again, this works fine.

What I need now, however, is to allow a "nil" selection for status (for entries that don't yet have or don't need a status assigned). I know I can do this in IB, but I'm finding that I don't have the foggiest notion how to do this in code. I tried binding to selectedValue with NSNullPlaceholder as a binding option (with a string of "Unassigned") but the compiler complains that "NSNullPlaceholder" doesn't exist.

Any help would be appreciated.

----

You know that the binding options is a dictionary, right? That would be the only reason I can think of off the top of my head for the compiler to fail. See my example below:

    
[issueDetailPanel bind: @"title"
					  toObject: foreignArrayController
				   withKeyPath: @"selection.number"
					   options: [NSDictionary dictionaryWithObjectsAndKeys:
						   @"InspectorTitle", NSValueTransformerNameBindingOption,
						   @"No Selection", NSNoSelectionPlaceholderBindingOption, nil]];


Look at NSKeyValueBindingCreation protocol in the API docs for a list of binding options that are valid. Once you read through them it's just a matter of matching everything up with the IB checkboxes until your binding acts the way you would expect.

