---
layout: page
title: OASplitView
---



A subclass of NSSplitView that adds auto-saving of the sizes of its subviews.
In <code>- awakeFromNib</code>, send the split view <code>setPositionAutosaveName:(NSString *)name</code>. It works just like the NSWindow <code>setFrameAutosaveName</code>. method.

*In the latest version of the OmniFrameworks, it also adds a method the delegate can implement to do something when the divider is double-clicked. (Like completely collapse a particular subview)

