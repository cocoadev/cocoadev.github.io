---
layout: page
title: CheckCompileTime
---

How to use check_compile_time() function and where is it ?

----
If your code was use with this macro you must test with #ifdef because it's implemented since 10.6 and does not exist into 10.5

Example

    
#ifdef check_compile_time
check_compile_time(sizeof(IPHeader) == 20);
...
#endif


JM Marino http://jm.marino.free.fr

