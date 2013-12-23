---
layout: page
title: NSSearchFieldMenuGoAway
---



I have an NSSearchfield, bound to be the filter-predicate for a Core Data data-source. All very normal. The delegate and Menu Template outlets are not set, and have never been.

Despite this, I can't seem to get rid of the little drop-down search box (where things like recent searches normally appear). I don't want there to be a menu available at all, but there is by default, with a single entry "Search" - the name of my predicate.

How can I tell NSSearchField to not have a menu at all? I have tried setting 'menu' and 'searchMenuTemplate' on the field and on the cell but it remains.

Thanks!

