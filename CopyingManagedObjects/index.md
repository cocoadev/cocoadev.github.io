---
layout: page
title: CopyingManagedObjects
---



Well, it's been a long two or three days, but I've finally got a working method to copy arbitrary graphs of NSManagedObjects (and subclasses). It relies on you having set ownership properly via the delete rule-- that is, a cascade delete rule says "I own this object", since the referenced object will go away if you delete the receiver. But that's all-- you just set up your model and run.

I've updated the code slightly and put it on github [http://gist.github.com/162745]. Note that I haven�t tested my changes beyond compilation, and this is at any rate old, somewhat messy code; your mileage may vary. That said, if you find bugs with it and fix them, I am happy to accept patches!

Enjoy! -- RobRix (Updated the link again. Sorry to all those who hit the old dead URL on my server�)

