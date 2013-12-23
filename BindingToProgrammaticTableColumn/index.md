---
layout: page
title: BindingToProgrammaticTableColumn
---



I want to bind an array controller to some table columns that I will generally be creating (or removing) programmatically.

Is it as simple as setting up the model class with an exposeBinding: for the appropriate keys and then sending a
bind: <binding> toObject: <my array controller> withKeyPath: <model key path> options: <options>
message to the table column whenever such a table column is created?

These table columns can be removed via user preferences, so I assume I will also have to send the unbind: message at that time.

I'm only asking because if it is much more complicated than that, I will not use bindings in this application, which is so far set up to use
the table data source methods from the Good Old Days.

Apologies in advance if this question falls under the "asked-and-answered" rubric.

----

You do not use exposeBinding in this way. In fact, for this problem, you don't use it at all. Check out the     <NSKeyValueBindingCreation> informal protocol's     bind:toObject:withKeyPath:options: method. This is exactly what you need. Nothing more. Just add the column to the table as you normally would, then bind it to whatever.

See Also: [ http://www.cocoabuilder.com/archive/message/2004/8/23/115385 ] and [ http://www.cocoabuilder.com/archive/message/2004/1/6/98427 ] and [ http://www.google.com/ ] (sorry, couldn't resist) :-)

Quick Example (error check this - it was typed right here in the wiki)

    
// Binding a "title" column to the array controller's arranged objects' 'title' property
// Assumes 'myColumn' and 'myArrayController' are valid
// Assumes no special options for the bindings (the little checkboxes and transformers in IB)

[myColumn bind:@"value" toObject:myArrayController 
                       withKeyPath:@"arrangedObjects.title" options:nil];

