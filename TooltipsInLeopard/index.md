---
layout: page
title: TooltipsInLeopard
---

  You must have noticed that tooltips in Leopard are not similar to the ones in Tiger. These are in fact the expansion tooltips, created by default in Leopard. If you observe clearly, these strange tooltips are generated in tableviews only in case the string is too long. 

  In cases where we already have a tooltip, these double tooltips may be annoying. To get rid of the expansion tooltips, just add the following delegate to your code.


----
//To disable default expansion tooltip in Leopard.


- (BOOL)tableView:(General/NSTableView *)tableView shouldShowCellExpansionForTableColumn:(General/NSTableColumn *)tableColumn row:(int)row

{
        return FALSE;
}
