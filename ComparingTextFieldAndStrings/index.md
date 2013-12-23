---
layout: page
title: ComparingTextFieldAndStrings
---



    if( [nameField stringValue] == [NSString stringWithString:@"name"] )...

I'm trying to check if the NSTextField contains the string "Name", and this doesnt work.  what am i doing wrong?

----

== compares pointer values.  You need to compare logical values.

    if( [[nameField stringValue] isEqualToString:@"name"] )...

