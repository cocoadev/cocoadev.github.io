---
layout: page
title: CustomViewsAsListItem
---

Doing some fair bit of searching, I am sure I still missed this one.

I have seen in some apps that one column contains a lot of elements (buttons, progress bar etc).
In IB I do not see this as a simply straight out-of-the-box available alternative. I have to create many columns and define buttons etc.

It seems that you can define a custom view and then load this as a list item in one column?
Could anyone point me in the right direction?

----

It would help if you'd use proper terminology in relation to this particular API. What do you mean by "List Items"? Are you referring to NSTableView, NSOutlineView, or what? If you're targeting Leopard and all your "list items" are homogeneously-sized, check out NSCollectionView / NSCollectionViewItem.

----

Sorry, my bad, it's the NSTableView, like the Download window for Safari, in there you see each item along with buttons for canceling or reloading. There is even a progress bar in the same column as the text.

----

