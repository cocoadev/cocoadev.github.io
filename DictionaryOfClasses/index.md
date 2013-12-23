---
layout: page
title: DictionaryOfClasses
---

I was wondering what the best way to store a dictionary of classes was.  I've tried putting a Class object in an NSMutableDictionary and it didn't work out too well.  Preferably I would want to hold the actual classes themselves and not their string names (to avoid having to do a lookup every time).

Thank you!

----

You could probably store it in an NSValue object which can be stored inside an NSDictionary. Or, just create a simple class that holds the Class object itself.

----

It works fine. Read HowToAskQuestions, PostYourCode, and actually say what is going wrong, otherwise it's going to be hard to help you.

The above got deleted when the other reply was posted. I hope this was just a mistake, as the question *is* poorly specified. In particular, there really is no actual problem with storing Class objects in dictionaries, as either value or key.

