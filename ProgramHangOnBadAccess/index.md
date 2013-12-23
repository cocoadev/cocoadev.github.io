---
layout: page
title: ProgramHangOnBadAccess
---

I am writing a program and the other day it suddenly hang during normal operation. I have done all what I could to try and reproduce, but without luck.

Today I then found a bug in my program which would cause one past the last element in a std::vector to be read -- so I have tried to access *v.end() in a separate program, but generally this does not lead to the behaviour I experienced. In general a random value is just returned (since the entire page of memory is probably granted to my task), except when no storage is allocated for the vector, but this instantly gives an exception (which abort the program) -- no hang...

Can anyone provide insight to when a bad memory access may result in a 'hang'? I do recall in the past that out-of-rnage memory access have stailed a program (rather than core dumping), but maybe this is very dependant on the mmu/memory setup/allocation etc.?

