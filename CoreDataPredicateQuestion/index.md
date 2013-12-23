---
layout: page
title: CoreDataPredicateQuestion
---

I have a set of CoreData objects (with an SQLite back end).  There is only one entity in the model.  That entity has as one of its attribute, a double typed unique number -- a number that no other entity instance has.  I would like to find the entity which has the smallest unique number greater than an arbitrary argument... and I would like to do so *efficiently*.  Do I have to build by own b-tree or something, or is there something I can do without re-inventing the wheel?  I've been trying to understand what the floor/ceiling/min/max "funcitons" in NSPredicate do, but the documentation is extremely poor, and I think I followed BNF description to the T and still haven't been able to get it to work... help!!!  Thanks!

