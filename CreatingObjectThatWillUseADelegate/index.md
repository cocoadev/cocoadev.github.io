---
layout: page
title: CreatingObjectThatWillUseADelegate
---

[ see also DelegationAndNotification ]

I am about to start writing my first big NSView subclass that will draw simple Gantt charts given some info. I would like my class to act a bit like NSTableView, using a delegate to describe to the GanttView how many projects it should display, and when these projects start and finish in time so that it knows how to draw the chart.

How do I achieve providing for the use of an external delegate. Is there anything special I must do? Or is it just that I should have a pointer to the delegate object of type id so that I can refer to it, and check that the delegate implements the correct methods with "respondsToSelector:" ?

Is there much I am missing here?

Any advice is really very much appreciated!

- Peter

----

You have the right idea. Delegates aren't magic or anything. You just have an ivar like     id delegate, then make sure it responds to the delegate messages before you send them.

One thing that will help document the delegate and make your code easier to write is to declare the delegate methods as an informal protocol, the way Apple does with most of its delegate methods as you can see in NSTableView.h and many other files.

----

Thanks for your help, I was hoping it wasn't more convoluted, and this is great news. Regarding using an informal protocol, I'm afraid this is not something I have experience with. Looking through the NSTableView.h header I think I've found what you may have meant:

    

...
...
... more of the header ...

@interface NSObject(NSTableViewDelegate)

- (void)tableView:(NSTableView *)tableView willDisplayCell:(id)cell forTableColumn:(NSTableColumn *)tableColumn row:(int)row;
- (BOOL)tableView:(NSTableView *)tableView shouldEditTableColumn:(NSTableColumn *)tableColumn row:(int)row;
- (BOOL)selectionShouldChangeInTableView:(NSTableView *)aTableView;
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)tableView:(NSTableView *)tableView shouldSelectTableColumn:(NSTableColumn *)tableColumn;

- (void) tableView:(NSTableView*)tableView mouseDownInHeaderOfTableColumn:(NSTableColumn *)tableColumn;
- (void) tableView:(NSTableView*)tableView didClickTableColumn:(NSTableColumn *)tableColumn;
- (void) tableView:(NSTableView*)tableView didDragTableColumn:(NSTableColumn *)tableColumn;

@end

... more of the header ...
...
...



Can you shed any light on why this is a good way of doing things? Is this code above indeed setting up an informal protocol... where does it fit into the scheme of things? Sorry that this is at the periphery of my knowledge at this moment in time.

Thanks for your help.

- Peter

----

You have indeed found an informal protocol. It's "informal" because it's actually just a bunch of method declarations that don't do anything. It makes life easier in two ways. First, it suppresses warnings when you write code like:

    [delegate tableView:self didClickTableColumn:column]

Second, it is a bit of extra documentation, so that somebody reading your .h file can see what the delegate methods are called.

----
To be more precise: an informal protocol is declared exactly the same way that you declare a category. It is thus like saying to the compiler: I am adding to the class     NSObject the following methods.
Categories can be for anything other than     NSObject, but an informal protocol only makes sense as a category of     NSObject, as you usually don't put any limitation in what a delegate can be.
After reading the category, the compiler is ready to accept that ALL objects can respond to the listed methods.

Of course, once you say that, you realize the dangers of it!  (which I realized a little late). An informal protocol is actually "polluting" the method name space. You don't want to use too basic names  like 'stop' or 'run' or 'isReady'. If any other class tries to use the same method name, with a different signature, you will have a compiler warning. Also, because it tells the compiler to expect any object to respond to these methods, it may miss some warnings. To avoid these problems, you should do like in the example you give: the method names start with 'tableView' as the first argument, and the text 'tableView' is part of the method name. It is very unlikely that other methods will ever be created that start with this prefix. It is actually really like prefixing class names to avoid clashes. --CharlesParnot

I don't know if you have read this (basic but useful):
http://cocoadevcentral.com/articles/000075.php

----

"The Objective C Programming Language" PDF in the documentation area of Apple's website explains protocols in detail.

-RobertMarshall

