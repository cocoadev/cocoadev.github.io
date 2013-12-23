---
layout: page
title: FrameworkVsWrapperDefinitions
---



Pardon my ignorance, but can somebody tell me the difference between a "framework" and a "wrapper". I have looked all over, and both terms seem to identify something specific, but I cannot find a definition of either. Specifically I am looking at building a PostgreSQL GUI to expand my understanding of Cocoa/Obj-C and develop a database to play around with. I have found references to a "wrapper" NKDPostgreSQL and a "framework" pgsqlcocoa. What would be the difference between the two? 

Thanks for any insight.
Philip

----

Wrapper is a generic term for anything that takes the functionality of one codebase and allows you to access it through another.  For instance, a PostgreSQL wrapper is any code that allows one to access PostgreSQL databases through Cocoa.  A framework is one specific way of distributing the wrapper.  A framework is a form of shared library, a code repository that can be shared between multiple programs.  Since a PostgreSQL wrapper is something that's an easy candidate for being shared between multiple programs, it's not unreasonable to guess that it is distributed inside a framework.  In short, wrapper is a generic term, while framework is a fairly specific thing. --OwenAnderson

