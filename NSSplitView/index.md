---
layout: page
title: NSSplitView
---


An NSSplitView object stacks several subviews within one view so that the user can change their relative sizes. A split view can "collapse" a subview when the user drags it past a certain point or double-clicks on a divider between two subviews. This provides a good way to allow the user to maximize available space for one subview while avoiding the situation where the other subview becomes uselessly small, with buttons collapsing onto each other, etc.

In Xcode 4, there are many splitviews, such as the main three panel splitter with the Navigator on the left, the editor in the middle, and the Utilities panel on the right. You can resize or collapse the Navigator and Utilities to maximize the space for the editor.

----


==Related Topics==

* SplitViewBasics
* AnimatedNSSplitView
* NSSplitViewWithAspectRatio



== Min and Max Subview Sizes ==

For a subview to have a minimum or maximum size, you must implement the NSSplitViewDelegate protocol's coordinate constraint methods.

To specify a minimum size for your subview, use the method     splitView:constrainMinCoordinate:ofSubviewAt:. The     proposedMin value is a coordinate (not a width/height value for the subview) in the splitview's (flipped) coordinate system, and will change depending on the size of the splitview itself. Rather than return a constant value (which will work only for a two-subview splitview), you can return proposedMin plus the point amount you wish to use as your minimum size. Below is an example that makes every subview a minimum of 60 points wide. 

 - (CGFloat)splitView:(NSSplitView *)sender constrainMinCoordinate:(float)proposedMin ofSubviewAt:(NSInteger)offset
 {
     return proposedMin + 60.0;
 }


The companion method,     splitView:constrainMaxCoordinate:ofSubviewAt: allows you to specify a maximum size for the view. Again, the same rules apply - proposedMax is a coordinate, so return a modified value of it. If you return a constant, you won't get what you want. Here, we make sure that any collapsible subview will leave 90 pixels for the other views. 

 - (CGFloat)splitView:(NSSplitView *)sender constrainMaxCoordinate:(float)proposedMax ofSubviewAt:(NSInteger)offset
 {
     return proposedMax - 90.0;
 }



== Collapsing Subviews ==

For a view to be collapsible, the delegate must implement a     splitView:constrainMinCoordinate:ofSubviewAt: to create a minimum size for the subview, and must return YES from     splitView:canCollapseSubview:. When the subview's divider is dragged a certain distance past its minimum contraining point, the subview will be be collapsed. Below is an example where the splitter has a NSTableView (inside an NSScrollView) as one of its subviews, and that tableView (like the Navigator in Xcode) can be collapsed. 

 - (BOOL)splitView:(NSSplitView *)sender canCollapseSubview:(NSView *)subview
 {
     return (subview == [tableView enclosingScrollView]);
 }


Note that the collapsed subview is retained by the splitview, with the same size it had before it was collapsed.

To find out when the view is uncollapsed, you can implement the delegate method     splitViewDidResizeSubviews: to be notified when the splitview resizes its subviews. Calling     isSubviewCollapsed: in this method will tell you if you should start updating the view again.


=== Collapsing on double click ===

To collapse a subview when its corresponding divider is double-clicked, implement the delegate method     splitView:shouldCollapseSubview:forDoubleClickOnDividerAtIndex:. This method allows you to pick which of the subviews adjacent to the divider to collapse.


=== Programmatically collapsing a subview ===

To collapse a subview in code, use     - (void)setPosition:(CGFloat)position ofDividerAtIndex:(NSInteger)dividerIndex method to set the position of the divider to be less than the constraining point for that divider (such as the point which would make the view 0 sized). If the delegate permits the view to be collapsed, it will be, and     isSubviewCollapsed: will return     YES. Note that setting the frame width/height of a view to 0 is not the same thing as "collapsing" a view. A 0 sized subview will still not report itself as collapsed.

To un-collapse a view, simply set the divider position to a value greater than the minimum divider position.



== Custom Subview Resizing ==

The standard behavior in NSSplitView when the splitview itself is resized, is to proporitonally add or subtract to the size of each subview. In a vertical splitview with two subviews, if the left subview is 20% of the width of the subview, when the splitview is resized to be 100 points wider, the left subview will be made 20 points wider and the right subview will be made 80 points wider. 

In some scenarios this default behavior is undesirable. For example, in a classic sidebar-mainview layout, when resizing the window, the sidebar width shouldn't change at all but with the standard NSSplitView behavior, it would be. To change this behavior the delegate can implement     splitView:resizeSubviewsWithOldSize:. By implementing this method, the delegate assume complete responsibility for subview layout when the splitview is resized, and thus can implement any behavior, such as only resizing the main view and leaving the sidebar width alone.

For open source code implenting multiple custom resizing behaviors, see [https://github.com/swillits/AGNSSplitView AGNSSplitViewDelegate]. For a simple example of priority-based resizing, see  [http://cocoawithlove.com/2009/09/nssplitview-delegate-for-priority-based.html this post on CocoaWithLove].



==External Links==

* [http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSSplitView.html NSSplitView Documentation]
* [https://github.com/swillits/AGNSSplitView AGNSSplitViewDelegate — Useful generic splitview delegate with multiple resizing options, and support for min/max sizes etc]
* [http://cocoawithlove.com/2009/09/nssplitview-delegate-for-priority-based.html CocoaWithLove Priority-based subview resizing]
* [http://cocoaobjects.com/rbsplitview/ RBSplitView — Custom from-scratch reimplementation]
* [http://homepage.mac.com/kenferry/software.html#KFSplitView KFSplitView — A useful subclass, but uses pre-10.5 approach.]
* [http://homepage.mac.com/jrc/contrib/ CollapsableSplitView — Adds collapseSubviewAt: Uses pre-10.5 approach.]

