---
layout: page
title: CategoriesAreGood
---

I believe there are a few distinct areas where CategoriesAreGood, and there are other areas where CategoriesAreBad. Under what circumstances are categories good?

-- MikeTrent


* Good for placing private methods so that they are not in the public h-file
* Good for extending a class' functionality; for instance, adding your own string methods to NSString instead of making them into functions or writing a new class. Another example: adding data compression to NSData.
* Absolutely required for AspectCocoa (Uh ... why is that a good thing? -- MikeTrent **AspectOrientedProgramming is nifty. Even if you don't want to use it, it's nice to have it supported.** Since categories cannot be overriden safely, AspectCocoa sounds like an example where CategoriesAreBad? -- MikeTrent **You want to talk to somebody who knows about that, I guess (: apologies.**)
* Using categories also speeds up compile times for large class implementations.  --zootbobbalu
* BillCheeseman uses them to split large controller classes into smaller files.
* (�more�)


See also ClassCategories for general discussion on categories and what you *can* use them for; after that, it's up to you to decide if CategoriesAreGood or CategoriesAreBad for your situation.

