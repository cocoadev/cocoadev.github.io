---
layout: page
title: FragileBaseClassProblem
---

When we write code like this:
    
struct Base
{
   int Foo;
};

struct Sub : public Base
{
   int Bar;
};

   ...
   Sub* obj = new Sub;


Then the compiler will generate a compound structure of all the classes, like this:
    
struct SubAndBase
{
   int Foo;
   int Bar;
};

   ...
   SubAndBase* obj = new SubAndBase; // actually Sub*


That way, only one allocation needs to be done, no pointers to base classes needs to be initialized, and member variables can be accessed in constant time (using an offset, rather than search a list).

The downside is that Base may be in some library, and Sub in user code, which is dynamically linked against the library. If a new version of the library is shipped, where the size of Base has changed (because instance data was added/removed), then all the user code needs to be re-compiled, to adjust for this new size (which will affect BaseAndSub) and all instance data offsets.

That is why we say, that we have a fragile base class problem, because any change of the base class, requires a re-compile of each sub class.

BeOS have this problem, because they have based all their frameworks on CeePlusPlus, so at least one system upgrade broke all user programs (and required them to be recompiled).

