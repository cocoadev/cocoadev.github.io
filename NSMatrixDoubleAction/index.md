---
layout: page
title: NSMatrixDoubleAction
---

I have an NSMatrix of NSButtonCells and actions sent on both single-clicking and double-clicking a cell. From the documentation of NSMatrix:

<code>
doubleAction
Returns the action method sent by the receiver to its target when the user double-clicks an entry, or NULL if there's no double-click action.

- (SEL)doubleAction

Discussion
**The double-click action of an NSMatrix is sent after the appropriate single-click action** (for the NSCell clicked or for the NSMatrix if the NSCell doesn't have its own action). If there is no double-click action and the NSMatrix doesn't ignore multiple clicks, the single-click action is sent twice.
</code>

As indicated above the double-click action is sent after the single-click action (the first click of a double-click) however I do not want this behavior. If the user double-clicks I don't want the single-click action to happen, only the double-click one. I am able to reverse the single-click action in the double-click action implementation however upon adding Undo support the single-click event is entered in the Undo stack which I do not want, again when the single-click is "part of" a double-click event. So, any ideas on either (1) somehow ignoring a single-click action if it's "part of" a double-click event or (2) removing the last item off the Undo stack or grouping the current item with the last item. Thanks for any suggestions.

DavidRoss

----
Actually, added Undo support makes this even easier. Just call     [undoManager undo] as part of your double-click action. Not only will the Undo event be removed, but the single-click action will be undone. --JediKnil----
----
Wow, thanks for the excellent tip! I just implemented that and it works beautifully. I'd actually tried this approach initially but had somehow needlessly complicated it. I love it when a clean fix is one line of code.

