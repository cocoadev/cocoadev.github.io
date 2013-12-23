---
layout: page
title: AspectCocoaRequirements
---

**AspectCocoaRequirements** - 

**Supported Pointcuts:**
*a single or limited set of (yet to be allocated) instances of a class or multiple classes  ACInstanceAspect
*all instances of a class or multiple classes  ACClassAspect

ACClassScope
*choose between the Class, MetaClass, or both.

ACClassFilter
*filter classes based on class name
*filter classes based on common inheritance
*filter classes based on common protocol
*filter classes based on responding to some method
*filter classes based on if they are part of the Cocoa foundation or application layers

ACMethodScope
*a single method on the chosen classes (and/or it's superclasses)
*multiple methods on the chosen classes (and/or it's superclasses)
*all methods on the chosen classes (and/or it's superclasses)

ACMethodFilter
*filter methods based on name
*filter methods based on return type
*filter methods based on argument types
*filter methods based on number of arguments

**Unsupported Pointcuts:**

*filter methods based on sender
*filter classes based on framework
*filter classes based on instance variables
*a single or limited set of (yet already allocated) instances of a class or multiple classes

**Supported Advice:**

ACAdvice
*Before
*After
*Around

**Unsupported Advice:**
*Adding instance variables to classes

