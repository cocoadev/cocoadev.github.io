---
layout: page
title: OutlineViewInNSTable
---

I have seen some applications that has a table view with nestled Outline Views / Disclosure buttons.

How is this possible? I cannot find any documentation on this.

Thanks

----

It's called NSOutlineView.

----

What I mean was a List / Table view that holds Outline views too.. I know there is an Outline view, but this only has disclosures right??

----

I'm afraid I haven't the slightest clue of what you're talking about. Can you describe it in some coherent manner, or provide a screenshot?

An NSOutlineView is just an NSTableView (it's even a subclass, imagine that) with the *possibility* for disclosure triangles. You aren't forced to use them everywhere. How does this differ from what you want?

----

Maybe he means something like TextBox? Just guessing here.

----
Thanks I will try to post a screenshot soon.. but as always a helpful forum!!
----

I think what you're talking about is the default behavior.  Do you think that an NSOutlineView is limited to only one level of disclosures? This is not true.  At any level there may be children of a line.

*And upon hearing this, the master became enlightened.*

Master? Them's awful big wurds fer a poster what spelt 'is topic wrong ... :-D  (Absolutely no offense meant, by the way!)

