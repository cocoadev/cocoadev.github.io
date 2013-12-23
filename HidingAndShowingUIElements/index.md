---
layout: page
title: HidingAndShowingUIElements
---



Hi there...

I'm looking for an easy way to manage UI elements. Basically, I want to hide and show groups of UI elements in response to different states in the Application (actually, it's a screensaver preference pane, but whatever). So if something happens, I want the Preference panel to look a little different, by temporarily hiding some UI elements and showing others.

I'm sure this is quite easy, I'm just having trouble finding what I want out there.

Thanks,
Alexandre

----
NSTabView.

----

Though terse, I agree this is the best approach. Try to group the elements common to all "configurations" or states separately and swap the rest with different tab view item in a "tabless" tab view. The state can dictate which tab view item is visible and you only need one copy of the "common" controls. In other words, don't completely recreate all the controls (the common ones and the ones that are state-dependent) on each tab, only the state-dependent ones. That'll make your life easier and your UI more organized.

----

Random person here. Thanks for this suggestion. I've also been hiding/showing elements. My IB was getting very messy.

----

Tab views are probably easiest, but there is another solution that's reasonably clean. Collect your related controls into a plain NSView as its subviews, then implement a simple category on NSView that implements -setHidden: by iterating over its child views with the same method. Because NSControl implements setHidden:, this gives you a quick way to turn a set on or off in one line. You can extend this idea to -setEnabled: too. This approach would allow you to have sets of controls showable or enabled within the same window or view, which tabs don't solve. That can sometimes be handy for dialogs, where one control enables a whole bunch of others. --GrahamCox

----
NSView itself implements setHidden: in 10.3 and up, so you don't have to do anything as long as you aren't supporting Jaguar. And if you are, what is wrong with you??

----

So it does - I didn't have the docs to hand when I wrote the above. Actually I was more thinking of the setEnabled: case, which of course is not what the poster was asking about, but grouping related controls and enabling/disabling them is very handy for the situation I mentioned. NSView doesn't have a setEnabled: but NSControl does, so adding this method to NSView using a category can save umpteen lines of code for some kinds of UI work. --GC

----

Yes, that would be useful. I would recommend giving it a less obvious name, though, such as     XYZRecursiveSetEnabled: though, just in case Apple adds     setEnabled: to NSView one day.

