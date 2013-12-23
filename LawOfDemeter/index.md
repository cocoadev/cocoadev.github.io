---
layout: page
title: LawOfDemeter
---

c2 describes it best:

http://c2.com/cgi/wiki?LawOfDemeter

The basic idea is that methods returning ivars are not in general as good an idea as e.g. methods operating on ivars, because you end up getting restrictive hierarchies of calls, e.g.     [[listOfThings objectAtIndex:0] someIvar] someOtherIvar] doSomething]} -- you can paint yourself into a corner this way, plus it's ugly.

----

I like the concept of this law, but I find it difficult to obey strictly. I find myself writing so many delegate methods it becomes impractical. Instead, I prefer to take a loose approach to this rule - write a delegate method if I find myself making a call two to three levels deep. I'd like to hear some success stories of those who have applied this rule strictly. Also, does anyone know of some good open source Objective-C examples that follow this? -- [[RyanBates

*Calling it 'Law' was probably a mistake, but there you have it. I'm definitely in agreement with the sentiment that it should be more of a warning light.*

**I apply the LawOfCommonSense when I am programming, it's a superset of KeepItSimpleStupid (KISS), LessIsMore (LIM), DontRepeatYourself (DRY) and many other pearls of wisdom from all those gurus whose career seems to be proportional with the spread of the internet. Please add to the list as you see fit! :)**

**I also follow the principle that people who write XYZIsBadArticlesAreBad -- Yes, I am in a truly jolly mood today ;)**

*All of these are excellent things. But LawOfDemeter and its ilk are RulesOfThumb-- common-sense, certainly. But, **even if nothing else**, it can be useful in the training of new disciples of the art. The important thing to remember is that programming is a skill, not an action, and that you need to use your noggin.*

LawOfDemeter is also called "Don't talk to strangers" and is part of GRASP.

