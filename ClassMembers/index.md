---
layout: page
title: ClassMembers
---

A couple times I have wanted to have the class have IVars and I have seen some examples with "global" values being used to hold the class's members, but at the moment I forget the details of declaring such things and thought it should be on this site... anyways another thing I was wondering about is if one could use the runtime functions to actually give the class's metaclass ivars... It would be a REAL pain I know to create a class this way but is it possible, or am I barking up the wrong tree. (because I could just create a parser to take slightly modified objc code and generate the class's runtime function equivalent.... I'm imagining the normal IVars prefixed with either - or + ...)

----

About the best you can do is declare a static global variable. In you implementation file:
    static id sharedVariable;

----

Hacking this in with runtime functions would probably be a lot more trouble than it's worth. Since a class variable by definition only occurs once and only within the context of a single class, static variables in the implementation file are a sufficient workaround. You could even do a     #define C**'lassVariable if you want to make some sort of distinction for them. I can think of a few cases where the difference would matter, but I think those cases would all be the result of questionable design.

