---
layout: page
title: NSDragOperationGeneric
---

What exactly does this mean?

What have me confused is that using modifier keys one can alter the drag operation:

*Control: NSDragOperationLink
*Option: NSDragOperationCopy
*Command: NSDragOperationGeneric


Normally data is either moved or it is copied (rarely linked, but that happens as well).

As a user I might be interested in sometimes changing a copy to a move or a move to a copy. But the qualifiers give me the ability to force a copy or force a "generic" operation -- so what the h... am I actually forcing my program to do, when I hold down the command key?

Ohh... and it is not made simpler by the fact that Finder always use generic as operation. So how can I know if the destination actually requested that the text would be moved? it would seem that I should only remove the data from the source if the operation was move or delete, and the destination should always favor move over copy, because using modifiers the user can change a move to a copy, but not a copy to a move. But this is really a bad default choice (move) in many situations.

