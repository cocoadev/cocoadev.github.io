---
layout: page
title: NSOutlineViewWithDifferingColumnWidthsBetweenChildAndParent
---

Is it possible to create an NSOutlineView where the columns widths of the child (and the number of columns) are different to that of the parent.

I have an interface problem where the parent data needs 5 (reasonably narrow) columns, but the child data needs only 1 (reasonably wide) column.

Does anyone have any clues as to whether this is readily achievable in NSOutlineView or NSTableView?

Can it be achieved by craftily changing the clipRect on cells?

----

Trees are best when parents and children have similar characteristics. See MailStyleNSSplitview for a way of handling data when the branches (mails and mail boxes) have many properties and the leaves (body of a mail) but one.

Do you know of an application which resembles what you want to create? I agree that this is an "interface problem".

