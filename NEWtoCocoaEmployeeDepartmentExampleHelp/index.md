---
layout: page
title: NEWtoCocoaEmployeeDepartmentExampleHelp
---

Hi, 

I'm an absolute beginner to Cocoa and I've done some of the basic Core Data tutorials on the web and wish to extend the experience.

I have made a simple example which adds employees & departments to NSTableView tables with a + button.

Is it possible to do filtering without having to write a line of code.

For example, I would want to click on a Department to show only the Employees in that department.

My example only uses 2 NSTableView tables to display the data.

I believe I need to use a Fetch Request but I'm really not 100% sure.

Any help or a guide in the right direction would be very much appreciated.

Thanks

Matt
----


----

What I would recommend to you, Matt, is that you learn the conventional ways of getting data in and out of interfaces. Learn about outlets and accessors, actions and delegates. Learn about ModelViewController. I know that things like CocoaBindings and CoreData make it much easier for experienced programmers to accomplish many of these tasks, but if you dive in like this, using techniques that make "programming so easy, even an idiot could do it" then you will be writing progams that only an idiot could write (namely, you will be producing the applications that the tutorials lay out for you). Just my humble opinion, but I think you are in a wee bit too much of a hurry.

----

"... you will be writing progams that only an idiot could write." - Hehehe ... what a way of putting it. Perfectly accurate, though. Even if you're a fairly accomplished developer, if you're new to Cocoa, you'll need to master the basics first. Bindings / Core Data is *not* a ticket for cutting corners in learning Cocoa.

----

Gee, that's neat. Grouchy me, I usually end up on your bad side (we've crossed horns on more than one occasion). It's gratifying that we can see eye to eye once in awhile. Thanks for the affirmation.


----
---- 

Thanks , I kinda, expected that sort of reaction, but nowhere in the documentation does it outline who this sort of trechnology is aimed at.
Conversely, there are a number of sites and tutorials expounding the virtues of Core Data and how you can build an app without a line of code.

----

The Core Data Programming Guide has a revealing topic, What Core Data Is Not, that says "Core Data is not an entry-level technology...." It says you've got to master the underlying technologies and techniques like memory management, KVC/KVO, etc.

http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdBasics.html

Alas, Core Data just isn't that newbie-friendly system to learn Cocoa. But Cocoa itself is a vast and wonderful world, so welcome, dig in, explore, and enjoy.

----

Is there any single web tutorial that you might recommend that really hits the spot?

What about this: http://www.cocoadev.com/index.pl?HowToProgramInOSX

best

Matt

----

