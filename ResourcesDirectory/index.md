---
layout: page
title: ResourcesDirectory
---



Ok. I've looked for info on this for the past 30-40 minutes and I feel as if I am wasting my time. Thus, a stupid question. 

How does one get the flipping Resources directory location? I have a file (html) that I want to load into a WebView. But I'm finding zilch in the Hillegass book and "resources" seems to result in a bunch of unrelated results on here, if there is info on it I missed it on here.

Thanks.

----
http://www.google.com/search?client=safari&rls=en&q=cocoa+resources+directory&ie=UTF-8&oe=UTF-8

----

For the record.. since the last reply wasn't all that helpful... yay for CocoaBuilder and luck.

You can get the application directory by using an NSBundle with the mainBundle message. This will return the application bundle... Using the instance method resourcePath will give you the path to the Resources/ directory... 

----
*You can also do something like this:     [[NSBundle mainBundle] pathForResource:@"index" ofType:@"html"]; --JediKnil*

----
Oh nice. thanks for the tip :)

