---
layout: page
title: ClosingNSBezierPath
---

I have a program that parses a data file and generates an NSBezierPath based on the coordinates it finds. Unfortunately, I do not generate these coordinate files and have to account for the possibility that shapes are not described with consecutive elements. This is easily overcome by prepending moveToPoint: messages before appending a line or curve, but doing so only helps me generate an outline. If I want to fill this path, the insides of curves and various slivers of the shape will be filled-- not the entire shape.

I don't really need to reinvent the wheel; if someone else has already done this please point me along. However, if anyone could help me conceptualize a proper flow to rearrange NSBezierPathElements to form a consecutively constructed closed path, I would greatly appreciate it. --adc

----

This is actually a moderately hard problem. The good news is I solved it just the other week ;-) If you take a look at my CONREC code (http://apptree.net/conrec.htm ), you'll see I had a similar issue when contours are generated - each line segment is generated out of order, so to form a coherent bezier path, they need to be resorted into order. The article I wrote there explains the issues and how to solve it. You might be able to adapt the code I wrote to your problem, depending on how the coordinates you're given arrive. You definitely want to avoid the situation where you need to brute-force sort the coordinates if possible, since there's no shortcut except a horrible O(n^2) search. My code won't do any better in the worst case which is is where coordinates are in a completely random and unconnected order, but chances are they are not, but are instead either mostly sorted or else scanned in some coherent manner. I;d be interested to know if this helps you out or not, or if you need any help adapting the code drop me a line. --GrahamCox.


----

That does help-- Thank you very much. It's very similar to my code, but mine is missing a few of the key ingredients there. I've been pressed for time lately, but I am working on merging some of your concepts in with mine. I'll post what I come up with here.

