---
layout: page
title: NSTextViewAndRTF
---

What is the easiest way to show the contents of a RTF file (or a plain text file) in an NSTextView? I was thinking something like having a window displaying the contents of a Readme file located in the app's bundle.

----

Something like <code>[myTextView readRTFDFromFile:[[NSBundle mainBundle] pathForResource:@"Readme" ofType:@"rtf"]];</code> is the easiest way to do this that I know of.  -- Bo

----

Thanks man, worked just fine. <-- Well if yer gonna edit my reply, the least you could do is replace the period at the end of my sentence.

