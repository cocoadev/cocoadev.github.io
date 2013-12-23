---
layout: page
title: UITableCell
---



Part of the iPhone General/UIKit framework. Subclass of General/UIView. (Unlike in General/AppKit..)

Abstract (?) superclass of whatever you'd ever want to display in a General/UITable. Useful subclasses include:


*General/UIImageAndTextTableCell


Those little right arrows? They're 'disclosures'. Use like:

<code>    [cell setDisclosureStyle: 2];</code>

<code>    [cell setShowDisclosure: YES];</code>

Where style 1 (default) is the round blue arrow and 2 is the basic grey arrow.

Row Selected Style:setShowSelection

<code>    [cell setShowSelection: NO];</code>

Setting setShowSelection: NO removes the text Highlite. YES is the default state.

Row Selected Style:setSelectionStyle

<code>    [cell setSelectionStyle: 1];</code>

Setting setSelectionStyle: 1 changes the selection color of the row to red
(0 || 4+ == blue, 1 == red, 2 == grey, 3 == green)
