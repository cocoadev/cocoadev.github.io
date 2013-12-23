---
layout: page
title: HowToRestrainNSTextViewToNletters
---

I have a NSTextView that is editable and i want to restrain the amount of letters or numbers the end user types into the box. 

Any pointers to how I would create this behavior would be great. I am newbe and have waded through ducuments for 4 days now to no avail.



----

How about implementing     - (BOOL)textView:(NSTextView *)textView shouldChangeTextInRange:(NSRange)affectedCharRange replacementString:(NSString *)replacementString;?

