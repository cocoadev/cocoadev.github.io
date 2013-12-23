---
layout: page
title: DisablingNSTableColumn
---



Can I somehow make an NSTableView (bound via Cocoa Bindings) column of checkboxes (all of them, the entire column) become disabled?

Can columns be disabled?

----

Programatical (is that even a word) solution for your first problem: Make your table column's header cell a checkbox itself; then, when the checkbox is clicked, change all other checkboxes in the column. You would have to remember to set a mixed state on this checkbox though, for when not all of the items are checked (or cleared). --JediKnil

----

How do I go about making the header a checkbox? This is via interface builder?

*...oh...um, whoever restored this, I was the one who deleted my post, because I misunderstood the question. I thought you meant unchecking the boxes instead of disabling them...like, say, Yahoo Mail or something. I don't *exactly* know how to set the header view to a checkbox (assuming that the table column's outlets don't include something like     headerCell), but the     setHeaderCell: method of NSTableColumn should work (never tried it, and can't because my system is totally messed up). You could try adding this to some controller class's     awakeFromNib: if you do want this behavior:*
    
tableView tableColumnWithIdentifier:@"checkboxes"] setHeaderCell:someCheckBoxCell];

*...where     someCheckBoxCell is a check box that you've set up somewhere in the nib file (you could even stick it in a top-level [[NSView and no one would see it), or configured manually. But this really doesn't solve your original problem... So, sorry I can't help more, and if anyone wants to re-delete my posts (since they don't really have much to do with your question), go right ahead. --JediKnil*

*P.S. Ironically, HowToDisableRowContentsInNSTableView has an answer that could be adapted for your situation.*

