---
layout: page
title: CoreAnimationAndNSTableview
---



Hi,

I was trying to get a NSTableView to animate like iChat with the help of CoreAnimation, but it seems impossible!
Since all the items in a Tableview is NSCells, CoreAnimation wont help at all...

Anyone got any input on how to do this??

----

Use an NSCollectionView instead?

----
Look at the code of Adium - they use animated table view, though it's not CoreAnimation. This should help you, too: http://www.mactech.com/articles/mactech/Vol.18/18.11/1811TableTechniques/index.html

Just try replacing [table ...] with [[table animator] ...].

