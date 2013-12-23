---
layout: page
title: NSDateFormatterWithEmptyString
---

While updating my code on 10.4, I decided to use the new version of NSDateFormatter, so that it would automatically take the locale information from the user's International formats preferences. I did this by switching to instances of NSDateFormatter that use the new 10.4 behavior. However, I have hit a snag. Everything works beautifully except entering a blank string in the Text Field, Table Cell, or whatever it may be. It does not allow the empty string to be a date of nil as the old behavior did. Is there any known way to turn this on? I am just using a simply instantiated NSDateFormatter programmatically as opposed to the old way of doing it through IB. Is there a step that I am missing? I can't seem to find anything in the class reference. Any help is much appreciated. -- LoganCollins

---- 

I am having the exact same problem and it is making me crazy!! -- Samuel

