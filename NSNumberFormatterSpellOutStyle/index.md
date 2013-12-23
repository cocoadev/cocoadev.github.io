---
layout: page
title: NSNumberFormatterSpellOutStyle
---

As a quick hint to anyone having trouble getting the NSNumberFormatter style NSNumberFormatterSpellOutStyle to work, you must first explicitly set the formatter behavior to 10.4. Apparently, the initial default formatter behavior on 10.4 is _not_ 10.4 behavior.

    
NSNumberFormatter *badSpellOutFormat = [[NSNumberFormatter alloc] init];
[badSpellOutFormat setNumberStyle:NSNumberFormatterSpellOutStyle];
NSLog([badSpellOutFormat stringFromNumber:[NSNumber numberWithInt:42]]); // ==> "42.00"
[badSpellOutFormat release];


    
NSNumberFormatter *spellOutFormat = [[NSNumberFormatter alloc] init];
[spellOutFormat setFormatterBehavior:NSNumberFormatterBehavior10_4];
[spellOutFormat setNumberStyle:NSNumberFormatterSpellOutStyle];
NSLog([spellOutFormat stringFromNumber:[NSNumber numberWithInt:42]]); // ==> "forty-two"
[spellOutFormat release];


-- JonathonMah

