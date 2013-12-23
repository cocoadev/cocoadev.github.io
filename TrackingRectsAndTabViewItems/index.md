---
layout: page
title: TrackingRectsAndTabViewItems
---

Does anyone know the best way to handle tracking rects when a view is in a tab view? The problem I'm having is that deselected views contained in a tab view are still tracking mouse movements. The only solution I have is to remove the view from the tab view and remove all the tracking rects (yes a view removed from its superview will still receive     mouseEntered: messages). There has to be a better way to handle this situation. Maybe creating a method like     enclosingTabView and having the view ask the enclosing tab view if it is selected would be the best solution, but this will require all views placed in a tab view to modify     mouseEntered/mouseExited. 

----

It would just require them to all descend from a common General/NSView subclass.

----

Hmm, I'd guess you have a bug somewhere.  A tabview only ever has one subview.  When you click on a tab, a tab view removes its current "tab" subview and swaps in the one corresponding to the new tab.  Your rects should be invalidated when the view's position in the hierarchy changes, and won't be reinstated in the same places because your views will no longer have any (view hierarchy) connection to the tab view.

Possibly you're overriding an General/NSView method somewhere and failing to call super where you need to, breaking the above paragraph?

Regardless, you should be able to do any necessary cleanup from     tabView:shouldSelectTabViewItem:, no?

----

Yeah, I just figured out that an General/NSTabView will remove the current selected view from its subviews and then add the new selection to its subviews. I'm now in the process of updating all view that add tracking rects to remove them when     removeFromSuperview is called.
