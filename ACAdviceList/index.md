---
layout: page
title: ACAdviceList
---

ACAdviceList - part of AspectCocoa

ACAdviceList objects can be compared to JoinPoints.  At any given time, there should be a one-to-one correspondence between advice lists and join points.  In fact it would not be incorrect to rename the class ACAdviceList to ACJoinPoint.  The difference is a matter of terminoligy.  We use the term JoinPoint to refer to a particular point in code where we would like to apply advice.  We use the term advice list to refer to a list of AdviceObjects that apply to a particular JoinPoint.

An advice list has 2 parts, the original IMP of the method it advices, and an ordered array of AdviceObjects.  When a ReplacementIMP is called, a lookup will result in a single ACAdviceList object. If the lookup does not return an advice list, then there is something wrong with the framwork (a bug), because a ReplacementIMPs should only be called in place of methods that have one or more aspects on them.  So, the job of the advice object is to return the IMP of the original method to the ReplacementIMP as well as performing the befores, and afters that exist on it's AdviceObjects according to the arguments it recieves from the ReplacementIMP.  (arounds are handled by the ReplacementIMP and the around-implementing AdviceObjects, ACAdviceList is responsible for returning all the AdviceObjects that implement around)

