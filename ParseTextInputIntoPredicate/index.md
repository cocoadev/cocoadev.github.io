---
layout: page
title: ParseTextInputIntoPredicate
---

Does anybody have any pointers (or code they'd be willing to share?) about how I could turn text input into an NSPredicate?  Example: the user types in John OR mary; I'd like to be able to turn this into [NSPredicate predicateWithFormat:@"name contains[cd] %@ OR name contains[cd] %@", @"John", @"mary"].

Nothing fancy, just the ability to use AND, OR, and the wildcard (*).  And to not get overly complicated I only envision all the values to be compared against going against a single attribute like @"name".  I'm just surprised that there's no way to have NSPredicate accept a string as an array of equivalent search conditions, or something.  I guess the fact that I can't even really express what I want in technical terms kind of explains why I can't expect it to already exist.  What do you think, should I try NSScanner?  or componentsSeperatedByString:?  I've been forking my efforts and I keep running into logical knots and nesting issues.  That's why i was wondering if there's maybe some simple parser already.

