---
layout: page
title: DisablingSpellCheckerForNSTextView
---



I'm having the problem that my NSTextView's always have the spell checker enabled.

Since the entries are not prose, this is frustrating to say the least.

It seems to be some bug in InterfaceBuilder, where it practically re-enable the �continues spell checking� if just I do one tiny change of the Nib. Since I have probably 10 NSTextView instances scattered out in different windows/nibs, it has gotten very tiring to have to inspect all these nibs/textviews all the time.

Is there a workaround for this problem?

----

Set it in code     [textView setContinuousSpellCheckingEnabled:NO]

