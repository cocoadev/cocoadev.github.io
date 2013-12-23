---
layout: page
title: NSTableViewQuestions
---

Ok, I want to have a NSTableView, and the items within the NSTableView, when unselected have a pure gray image, and when selected display a white image. How would I accomplish that?

----

Josha I assume?

It's quite easy to do, just use a custom nscell for the row's in the tableview and draw in different colors when highlighting.

There is some code in NSTableViewTutorial to show you how to handle highlighting. -- MatPeterson

----

Thanks Mat, another quick question. In Interface Builder, when I drag a NSImageCell from the Data palette into an NSTableView, when I enter the image name, it just clears itself, did I miss something? This is bothering me.

--JoshaChapmanDodson 

----

I don't know the answer for that one as I don't use it because its incompatible with Jaguar. I am sure someone else can fill you in though. -- MatPeterson
----
Anyone?
----

 If I want to make an NSTableView perform an action when I double click an item, I know I need to set the delegate and turn off editing, but I'm stuck from there.  It would be helpful if you could tell me what to look for and how to implement "alternative actions."

*Use NSTableView's -setDoubleAction: for this. The target can then decide what double-click action to perform.*

