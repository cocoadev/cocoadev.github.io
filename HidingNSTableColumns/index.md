---
layout: page
title: HidingNSTableColumns
---

Anybody knows how to hide and show programmatically NSTableColumns? It isn't apparent how to do this from the docs, although I suspect I have to remove the objects from the table columns through NSTableView's methods. Is that so?
----
Yes. To hide, remove the column completely. To show, reinsert it. Remember to retain it before taking it out, otherwise it will be destroyed.
----
Thank you thousand for the clarification. -- l0ne

