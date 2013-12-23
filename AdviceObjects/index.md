---
layout: page
title: AdviceObjects
---

AdviceObjects - 

We use the term AdviceObject to refer to an object that contains advice.  In AspectCocoa our advice objects will generally respond to some variation of *before:* or *after:* .  *before:* and *after:* will get called on all AdviceObjects that have been assigned to a given PointCut and whenever a JoinPoint within the scope of that PointCut is encountered during the execution of a program.  In general, AdviceObjects are applied to PointCuts via the creation of Aspects.  The functionality of *before:* and *after:* will vary depending on what purpose the Aspect was created for.  But a most simplistic example is the ACLoggingAdvice class.

see AdviceObject for specific usage.

