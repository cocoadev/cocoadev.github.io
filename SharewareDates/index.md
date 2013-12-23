---
layout: page
title: SharewareDates
---

I'm creating a shareware program that I want to quit working 10 days after it is run for the first time. I have set up the program so that it creates a dictionary and writes the date to a file in the NSDate format the first time the program is run. 

My question is:

Is there an easy way to compare the date the program was run for the first time to the current date?

----

See NSDate docs for the     - (NSTimeInterval)timeIntervalSinceNow method.

