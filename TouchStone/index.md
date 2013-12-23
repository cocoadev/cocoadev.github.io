---
layout: page
title: TouchStone
---

Touchstone is not a set of additions to General/LodeStone. Instead, it is a music synthesis and composition program based upon a simple axiom, 'everything is a functor'. You could just as easily say 'unit' or 'module' rather than functor, it's of little matter. Lodestone has a similar axiom, but differs a fair amount in the implementation; it may bear rewriting to be more like General/TouchStone. (Or else I'll just generalize functors into some General/CXLCore thingy and release them into the wild.)

One of the interesting developments from its, er, development, has been my writing the functor support system entirely in C (for efficiency) with much of the interface to said subsystem written in a couple of classes (for niceness). A functor is a structure which includes, among other things, a list of subfunctors, the size of the overall functor (for reasons which will become apparent), and a pointer to a function which does the 'magic'.

Functors tend to require 'ivars', and since I haven't yet generalized those into the base functor struct (and am not sure that I will, it's a fairly complex task), I have a rather more interesting solution; specific functors are defined as structures the first member of which is a General/CXLFunctor. Due to that, pointers to e.g. General/CXLSineGenerator become equivalent to General/CXLFunctor since the only interface functors need is defined by the latter. It's much like inheritance, and using it gives one an appreciation for the fact that in C, you're only ever one byte away from disaster.

There's a bit more complexity than just that; functors also have an 'outer' function which applies their workhorse function to each of their arguments' channels and whatnot (since audio is surprisingly multidimensional), but the general point of this is...

Touchstone is really a domain-specific language without a syntax hiding inside a .app.

And I think that's worth talking about.

-- General/RobRix
