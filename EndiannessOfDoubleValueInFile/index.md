---
layout: page
title: EndiannessOfDoubleValueInFile
---

I'm trying to parse a file which contains little-endian double values in a way that works on all varieties of Mac OS. It seems there are a bewildering array of endian swappers available, but nothing that can quite deal with this situation, which tells me I've missed something obvious! I thought NSSwapLittleDoubleToHost would be the one, but it accepts a type called an NSSwappedDouble, not an ordinary double. Is it just a case of casting it? Trying that with other similar functions such as EndianS64_LtoN doesn't work properly - I either get scrambled values that are very very large or very, very small, or else truncated to an integer. --GrahamCox

----

Looks like all I had to do was cast it:     double x = NSSwapLittleDoubleToHost(*(NSSwappedDouble*)&myFileValue);. Tracing through, it internally will call CFSwapInt64 on a big-endian machine, but the various wrappers seem to avoid the type promotion/demotion that I was getting when calling the function directly. (Sidenote: I wish people would *think* before writing machine-architecture specific types to a file - in this case one that that is very widely used for data exchange since about 1990, so it was probably thought up by some DOS-head who assumed. Still, at least it's documented.) --GC

