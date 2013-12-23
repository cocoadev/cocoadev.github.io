---
layout: page
title: KeyViewLoopGuidelines
---



The key view loop can be problematic to deal with. It is designed to just work magically, so in most cases it's not an issue. But if it doesn't work, it's pretty difficult to figure out why not. Here are some guidelines for getting a working key view loop.

1. Consider whether you can settle for an automatically generated key view loop. Each responder's top left corner determines its placement in the loop. The loop proceeds from upper left to lower right, row by row (at least for left-to-right scripts). This is by far the easiest solution. To enable this, make sure the window's initialFirstResponder is nil. See also -[NSWindow recalculateKeyViewLoop].

2. If the automatic key view loop is not suitable, set up your own key view loop using Interface Builder as much as possible. The window's initialFirstResponder outlet *must* be set, in order to disable automatic key loop generation. From that responder around the loop, set the nextKeyView outlet of each item in the loop. (If desired, the last item's nextKeyView can be set to the first item, thus closing the loop.) For any view with scrollbars (NSTextView, NSTableView, etc.), you should use the enclosing NSScrollView when setting nextKeyView.

3. If you have any responders created in code, splice them into the key view loop early (preferably in awakeFromNib or maybe - [NSWindowController windowDidLoad]). For each (sequence of) new item(s), you must call -[NSView setNextKeyView:] once to make the previous item point to the (first) new one, (calls to make each new item point to the next), and finally to make the (last) new item point to its successor.

4. If your window has a toolbar, toolbar items that are interested in becoming key view will automatically add and remove themselves as the toolbar is shown or hidden. The toolbar does not take into account the return value of -[NSWindow autorecalculatesKeyViewLoop]. Toolbar items are always placed in the loop before the top leftmost item. There is no easy way to change this.

5. Once the window has been displayed, it can be extremely difficult to modify the key view loop - in particular if you are using NSScrollView or NSTabView. These (and others?) are special cases because they insert their contained views into the loop automatically. For information on the initialFirstResponder and key view loop of an NSTabViewItem, see the AppKit release notes for OS X 10.1 <http://developer.apple.com/releasenotes/Cocoa/AppKitOlderNotes.html#X10_1Notes>.

6. If you have items that should sometimes be in the loop and other times not, it is not advisable to attempt to splice them in and out of the loop. Instead, subclass -[NSResponder acceptsFirstResponder] for these items. If an item returns NO from this method, it will be left out of the loop (temporarily); if it returns YES, it will come back into the loop. Alternately, if the item is derived from NSControl (it probably is), you can call setRefusesFirstResponder: on it.

7. If you make a mistake, your key view loop will cease to function, either in one direction or in both. Once it breaks it stays broken. To debug, comment out calls to setNextKeyView: or setInitialFirstResponder: until it works again. The offending call is likely trying to modify the key view loop in the presence of NSScrollView or NSTabView, after these objects have already done their behind-the-scenes loop-munging. Move the calls to an earlier point, or do without. (If you have no calls to setNextKeyView:, then check your nib - make sure the window's initialFirstResponder is set and that nextKeyView outlets are chained together the way you want.)

8. In System Preferences/Keyboard & Mouse/Keyboard Shortcuts, at the bottom of the pane under "Full keyboard access", you can control whether key view loops include all controls or only text fields and scrolling lists (^F7 to toggle). You should test your key view loops with this setting in each state.

These guidelines were determined by experiment and may not be entirely accurate. Corrections and further explanations are most welcome.

(Same text posted to cocoa-dev@lists.apple.com)

-- JustinBur

----

Notes 5, 6, 7 are crucial if you have dynamic single-window UI where views could be replaced (e.g. master-detail UI with different possible detail views). I struggled a lot to update key view loop, but it was always breaking after one detail view was replaced by another. I ended up with custom actions to jump to next/prev views using arrow keys and relying only on responder chain.

-- OlegAndreev

----

-setAutorecalculatesKeyViewLoop and 2 other NSWindow methods were added in 10.4, see "NSWindow key view loop" in <http://developer.apple.com/releasenotes/Cocoa/AppKit.html>

JustinBur, thanks for this guide. -- PaulCollins

