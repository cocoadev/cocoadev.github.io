---
layout: page
title: QuestionOnNSTabView
---

I have an NSTabView with 2 items (one, two). My question is: How do I find out which tab the user clicked on?
I tried to draw a connection between the tab and the object controller, which didn't work.

----

To retrieve the selected item at any time:

    - (NSTabViewItem *)selectedTabViewItem

Returns the tab view item for the currently selected tab, or nil if no item is selected. If there is a delegate and the delegate supports it, send the delegate the     tabView:shouldSelectTabViewItem: message.

To perform an action when an item is selected, implement

    - (void)tabView:(NSTabView *)tabView didSelectTabViewItem:(NSTabViewItem *)tabViewItem

in your tab view's delegate and check     [tabViewItem identifier] to determine which tab is selected.

----

Could you point me to some sample code or a tutorial on how to use the delegate? ( I know what it does but don't know how to implement it).

----

It's just the same as using any delegate - drag from the tab view to your controller object in IB, and connect the delegate outlet, or do     [tabView setDelegate: self] in your controller object's code. Here's Apple's docs on the subject:

[http://developer.apple.com/documentation/Cocoa/Conceptual/CommunicateWithObjects/Articles/Delegates.html]
----
I'm struggling with another problem:
didSelectTabViewItem is only called twice and then never again. Even using [tabView setDelegate:self] in didSelectTabViewItem doesn't help.
I don't understand what is happening. 
It is not only happening with one tabView but two. They each have another delegate but didSelectTabViewItem is only called twice.
Any ideas?
Could this be a Tiger issue or a bug?
I'm using MacOS 10.4.2
----
Is it still a problem?  I'm using 10.4.8 and am able to receive didSelectTabViewItem method calls more than twice with two tabViewItems, setting the delegate  only in IB.

