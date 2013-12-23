---
layout: page
title: NSClassCustomSubclassBindings
---

I want to design a project management application, and I need to draw a "Gantt Diagram" in a view. To do so, I use a NSTableView, and I created a custom NSCell subclass (M<nowiki/>yGantCell).

M<nowiki/>yGantCell is used to draw an "Gantt bar", from 4 values :


*the begining date of the task
*the ending date of the task
*the begining date of the project calendar
*the ending date of the project caldendar


Then, I set an instance of M<nowiki/>yGanttCell as the NSDataCell of the "Gantt" column of the NSTableView :

    
myCell = [[MyGanttCell alloc] init];
ganttCol = [myGantt tableColumnWithIdentifier:@"ganttBars"];
[ganttCol setDataCell:myCell];


But then, I'm lost with the bindings : data being the NSArrayController that controls the data source (a CoreData model, with an Entity "Task" and 3 attributes : taskname, begin, end, I wonder how I can bind these data to the NSCell instance or to the NSTableColumn ?  I tried :

    [myCell bind:@"begin" toObject:data withKeyPath:@"arrangedObjects.begin" options:nil];

or

    [ganttCol bind:@"begin" toObject:data withKeyPath:@"arrangedObjects.begin" options:nil];

without success. Any idea ?

----

*Have you tried this? If it doesn't work, you might want to consider using a custom NSFormatter. --JediKnil*
    [ganttCol bind:@"value.begin" toObject:data withKeyPath:@"arrangedObjects.begin" options:nil];

----

I looked at the way the NSSliderCell works with bindings. In IB, in the NSTableColumn inspector, you can bind the maxValue, the minValue and the Value of the to some attributes of an Entity : this works perfectly.

Now programmaticaly  :

In http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/Tasks/masterdetail.html#//apple_ref/doc/uid/20002090, it says : "you actually configure the bindings of the table columns, not the bindings of a table view. Even if you use a special type of cell in the table column, for example, an image cell or NSPopUpButtonCell, you configure the table column, not the cell. The bindings for an NSTableColumn may change depending on its cell."

After setting an instance of NSSliderCell as the NSDataCell of the "Gantt" column of the NSTableView, the exposed bindings of this NSTableColumn actually change from :

 exposed bindings =(
    fontSize,  maxWidth, fontName, 
    minWidth, 
    enabled, 
    fontFamilyName, 
    headerTitle, 
    font, 
    fontItalic, 
    textColor, 
    value, 
    displayPatternValue1, 
    width, 
    editable, 
    fontBold
)

to :
exposed bindings =(
    maxValue, 
    minWidth, 
    enabled, 
    headerTitle, 
    minValue, 
    value, 
    editable, 
    width, 
    maxWidth
)


but this doesn't work with my custom NSCell subclass ...

