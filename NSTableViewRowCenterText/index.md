---
layout: page
title: NSTableViewRowCenterText
---

I have a NSTableView:
http://devboys.com/images/nstableview.jpg
See how the text is slightly upwards, and not centered.
I would like it centered. What would I do, I tried everything with IB.

----

Subclass the NSTextFieldCell.
----
It's subclasses as ImageAndTextCell

----

Figured you didn't write the code yourself. I would get a brand new NSTableView, add two table columns, make one an NSImageCell and the other a regular NSTextFieldCell and see if you can make it work. If you can't, go back to basics, I think you need to relearn them.

----

This is just a guess but have you looked at the WHOLE image? Perhaps it has a shadow or other unseen pixels that are causing the whole cell to be larger than you would expect.

----

Why not use an NS(Mutable)AttributedString (since you can lower/raise the base line)? You can stick images in the attributed string too (don't need an extra column) but maybe you knew that already.

Something like this...
    
{        // didn't test the value here
          NSDictionary *defaultAttributes = [NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:-0.1] forKey:NSBaselineOffsetAttributeName]; //autoreleased
          mAttedString = [[NSMutableAttributedString alloc] initWithString:@"" attributes:defaultAttributes]; // kept around
}

- (void)dealloc
{
   [mAttedString release]; // balanced
   [super dealloc];
}

- (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
  NSString *stringForRow;

   [mAttedString replaceCharactersInRange:NSMakeRange(0,[mAttedString length]) withString: stringForRow ];

return mAttedString; // hand it off
}

