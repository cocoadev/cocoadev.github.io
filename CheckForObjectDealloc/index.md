---
layout: page
title: CheckForObjectDealloc
---

Is there any way to see if a certain object has been dealloc'd before sending a message to it?  I have a retain-circle problem, so am having one of my object not retain it's target, and then just not do anything if the target has been released/dealloced.  However, I can't seem to find any way outside of testing for nil (which isn't 50% certain) to see if my object is still around.  Is there a way I can do this?

----

When an object is deallocated the memory is re-used, so you can never test something which has been freed!

You could make the object unregister itself. Perhaps using a notification if you cannot tie the objects together.

----

WeakReferences may be useful for you.

