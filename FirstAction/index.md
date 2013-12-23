---
layout: page
title: FirstAction
---

is there anyway to get a program to invoke a button action or a menu action immediately after starting?

----

    

- (void)applicationDidFinishLaunching: (NSNotification *)aNotification
{
    [myButton action:nil];
}


----
how is this code called?

*Put it in your app delegate. If that won't work for you, put it in an appropriate awakeFromNib or windowControllerDidLoadNib method. These will be triggered whenever your nib is loaded, however, not specifically when your app is launched.*
----

When the application finishes launching, you might not necessarily have a window up for you to do stuff with.  Either do your action triggering in an appropriate awakeFromNib, or invoke the method directly

*You don't need a window up to call a control's action - the outlet simply needs to be connected, which will be the case in applicationDidFinishLaunching, assuming proper scope.*
----
I created an awake from NIB method that allows me to perform things immediately after launching which is good...thanks for that.

[myButton action:nil];//did not work though.

i am not familiar with calling a controls action.

i know i can call a method like [self myMethod];

to call a buttons action is there a connection that needs to made in IB?

if so....from what item do i connect to the button?

thanks!!

----

