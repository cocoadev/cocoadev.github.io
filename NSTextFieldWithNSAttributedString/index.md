---
layout: page
title: NSTextFieldWithNSAttributedString
---



I have a NSTextField and I set its attributedStringValue.  When the view is first displayed, the text of the NSTextField looks as it should.  But as soon as I tab into the NSTextField, the text attributes are lost and they stay lost even after ending the edit.  What am I doing wrong?

----

PostYourCode is one way we advise you on HowToAskQuestions.

----

Look up <code>-[NSCell setAllowsEditingTextAttributes:]</code>.

