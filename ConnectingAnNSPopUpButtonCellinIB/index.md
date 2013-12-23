---
layout: page
title: ConnectingAnNSPopUpButtonCellinIB
---



I have an NSPopUpButtonCell attached to a table column and I need to change the bindings of it programatically. However IB won't let me connect it to an outlet on my File Owner class and it keeps selecting the table column header instead? Does anyone know how you can connect them to an outlet?


*Select the small triangle in the top right corner of the column header, and use that to connect to your outlets.*

----

I see how you can connect other elements to the outlet of an NSPopUpButtonCell attached to a table column, but what about the other way around? What if I want a controller to perform some action on the NSPopUpButtonCell (for example, sort its items) as it gets selected? I would imagine I need to connect the NSPopUpButtonCell to the outlet of that controller, but in IB it seems impossible to Control-drag to an NSPopUpButtonCell in a table column (I always end up selecting the scroll view). Any help greatly appreciated.

----

Try using the list view of the nib instead of the icon view. Click the teeny little icon above the vertical scroller in the nib window.

----

Brilliant! Never knew about this feature. Thanks a bundle.

