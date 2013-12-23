---
layout: page
title: NSTableViewTransparency
---

Is there anyway to make a NSTableView's background transparent.  I've been messing with it in Interface Builder, and when I'm designing the window it is transparent, but then when I test the interface the background is just solid white.  Any help would be appreciated.. Thanks in advance.

----

I would like to do the same thing.  I was trying to make an NSTextView's background transparent earlier, and I discovered that I had to programmatically call setDrawsBackground: on both the text view and it's enclosing (scroller) view.  I tried to do the same thing with NSTableView, but it doesn't have such a method.  If anyone knows how to work around this, please post...  :-)

----

I wound up using an NSMatrix instead of a table view.

----
I was able to get a *seemingly* transparent table with the following...

    
- (void)awakeFromNib {
  [tableView setBackgroundColor:[NSColor windowBackgroundColor]];
}

- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell 
      forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex {
  [aCell setDrawsBackground:NO];
}

