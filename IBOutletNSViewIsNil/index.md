---
layout: page
title: IBOutletNSViewIsNil
---

I have two projects.  One uses a toolbar to switch between two views that can be displayed in the main part of a window. Kinda like a tab view but the views are selected by toolbar items (and menu items) instead.  This project works fine.

I tried to do the same thing in a second project and can't make it work. I have outlets on my controller class that are connected to the views (and yes, I quintuple-checked, they really are connected).  At runtime, the outlet variables are nil, verified by the debugger in awakeFromNib.  I changed the views to use a custom subclass and put an NSLog in their initializers and the NSLog is being printed; the views are being instantiated, but they are NOT being hooked up to my outlets, in spite of the fact that (yes, I sextuple-checked), Interface Builder shows them as being connected.

Any hints would be greatly appreciated.

-- Ben Cox

----

I have a funny feeling I've seen (and solved) this one at some point in the past. Working with what little information you have provided,
I suggest three possibilities. (1) If your views are subviews of the window in the nib file, I cannot see why the outlets are nil at runtime.
(2) If they are not (that is they are custom view objects instantiated separately in your nib file), you may need to use something like 

    
	// "What I want is a view" -- Hannibal Lecter, 'Silence of the Lambs'
	[ theWindow setContentView: theSubView ];


(3) I can't think of a third possibility.

-- Dan

----

Thanks Dan.  It's (2); they are custom view objects instantiated separately in the nib file.  I was doing the setContentView: call and
not seeing them, which is why I checked in the debugger and found that theSubView was nil.  I just can't figure out why they're nil, since
they are being created, and the connections do appear in IB in the nib.

Thanks though.

-- Ben

----

Solved this.  Boy, do I feel stupid now.  The solution to this one hit me in the forehead like a truck.  DOH!

Each view was connected to an IBOutlet named "viewX", "viewY", "viewZ", etc.  The controller
also had an action to show each of the views, which was connected to a menu item and to the toolbar item's action selector.  The
actions were named "setViewX", "setViewY", etc.  When a nib is loaded and an outlet connected, first IB attempts to set the outlet
using a "setOutletName" method, before setting the outletName instance variable directly.  Since my methods were named setViewName,
it was calling them; since they were present, it called them and figured it was done.  Since my methods didn't actually set the instance
variables, they never got set.  Hence nil.  This also solved the mystery of why "setViewX" was getting called on my object before I
called it to set the default view in the awakeFromNib method...

Renaming the methods to "showViewX", "showViewY", etc., fixed the problem.

-- Ben

----

Don't be too hard on yourself. Who hasn't been tripped up by namespace issues...?
Like it is for pilots: There are those that have made gear-up landings, and those that will...

Just recently, I was snookered temporarily by naming some data ivars data1, data2, etc. in a KVC construction,
eventually to be directed to some obscure Cocoa header that had already claimed them.

-- Dan

----

I wish I had found this posting last week (2008-06-20); I had the same thing happen, and Apple responded to my bug (#6024258) that they'll mark it as a duplicate, requesting to improve the usability of this kind of thing by providing a warning when something like this happens.  So, if you've been bitten by this, go and add a bug also requesting the warning to be added.

-- Gary

