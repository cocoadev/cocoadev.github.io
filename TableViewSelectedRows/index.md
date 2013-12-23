---
layout: page
title: TableViewSelectedRows
---

My app has a window that contains two table views, Main and Subsidiary. By selecting rows in the subsidiary table, certain rows in the main table will be selected automatically, with a relation between the content of the selected rows in the subsidiary table and the content of certain cells in the rows of the main table. It's a crude attempt at a filter allowing multiple selection criteria, instead of trying to parse regexes or something in an General/NSSearchField. The subsidiary table in fact simply shows the set of unique entries in a certain column of the main table.

Things seem to be working well so far. The main table live-selects the rows I expect it to when I select rows in the subsidiary table. However, since whilst I am doing this, the focus is on the subsidiary table, the selection appears dim in the main table, rather than in the default row highlight color. Row operations based on selections in the main table all seem to work fine. Of course as soon as I click the main table to return the focus to it, that selection is destroyed.

What I have done so far to accomplish this is so simple, works so well, and requires so few lines of code that I hesitate to embark on a more complicated scheme unless I am somehow creating disastrous confusion as to what this filtering is allowing the user to do.

The only thing that is bothering me is the background color of the selected rows in the main table during filtering. I'm looking for particular suggestions on how to manage the view so that it obeys reasonable HI guidelines in letting the user know that selected rows in the main table are available for manipulation. Could I simply change the background color of the main table to something bright and cheery, and switch it back when the filtering is finished? I just don't know if that kind of thing is frowned upon in the Guidelines. I guess this is just the latest missing piece in my continuing education...

----

I don't entirely understand all of your text, but I think perhaps you want to subclass General/NSTableView for your subsidiary table and override acceptsFirstResponder to return NO.  You'll still be able to make selections in the subsidiary table, but keyboard focus will stay with the main table, so selected rows of the main table will be the correct color.

----

I think you understand more than well enough. I will subclass the subsidiary table view since its single column is not editable by the user, and depends for its contents only on entries in one of the main table columns. This strikes me as being the most HIG-compliant, and should work adequately, since anything the search field is doing will be cancelled by filtering in the subsidiary table. I'll report back only if additional issues appear. This opens other cans of worms, since I have other objects that can get keyboard focus, like a search field in the main view and some text fields in an inspector panel, but this is not as much of an immediate problem. I just haven't had to think about them so far. Many thanks for your sound suggestion.
