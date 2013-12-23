---
layout: page
title: ConstantCArray
---

It's not Cocoa related, sorry...

I want to create a constant multi-dimensional array (2 dimensions) with arbitrary defined characters in it. I want to be able to access it everywhere, not just in one function. I tried hundreds of times, but can't compile without errors...

----
Like this?

In some common header...

    
extern const char charArray[3][8];


In some source file...

    
const char charArray[3][8] = {
{'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'},
{'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p'},
{'q', 'r', 's', 't', 'u', 'v', 'w', 'x'}
};


----
Excellent, it's working now...

