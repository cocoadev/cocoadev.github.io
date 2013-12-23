---
layout: page
title: DelegateObject
---



A delegate object is AnObject which acts on behalf of another object by implementing ObjectMessages which allow the delegate to interfere in the normal flow of events, unlike notifications which only allow the receiver to react to the event.

Delegates are typically used to avoid subclassing.  For example, it is rarely necessary to subclass NSWindow.  If you need to intercept window close events, resize events, or so on, this can be handled by a delegate.  The delegate can be any other object of any type that responds to the messages sent by the delegate.

The exact names of these delegate methods can be found in the documentation for the delegating object.

Example:

If you create an NSBrowser in InterfaceBuilder, it does not know about its contents at all.

Instead of declaring the content explicitly in InterfaceBuilder, you just declare which object is going to be the DelegateObject for the NSBrowser. (You could set this during runtime, as well.)

Now when the NSBrowser is getting ready to be displayed at runtime, it sends a message to its delegate that it is about to be displayed.

The DelegateObject then does everything necessary to fill the NSBrowser with the correct content.

A more thorough explanation can be found in this article:
http://www.stepwise.com/Articles/Technical/2000-03-03.01.html

Delegation is similar, but not identical, to TemplateMethod DesignPattern, though it can mimic it with the use of FormalProtocols.

----

There's nothing magic about it. For example, a table view will 'delegate' some of its decision-making to other objects. You tell the tableview that the object MyController is its delegate and the table view from that point on will ask MyController to answer its questions -- the table view sends one of its delegate methods to the MyController - it's your job to make sure to implement that method in your MyController source. MyController answers the question for the table view.

 For a direct example of a delegate method for a table view, check out the HowToDisableRowContentsInNSTableView discussion. The second code example (as of 4/1/05) is one of NSTableView's delegate methods. In the example above, MyController would have to implement that method. The table will then call that method (sending the mesage to MyController) and MyController will decide what to do when that particular cell will be drawn. That's exactly what delegation is for.

 That's it in a nutshell. There are plenty of other topics (besides HowToDisableRowContentsInNSTableView) that cover this in depth.

More importantly: NSTableView uses a data source, or you can bind your table columns to variables in a data model.
A data source is
an object that takes the responsibility of containing and serving the data that an object (such as a tableview or its descendants) displays.  usually used in conjunction with matrices.

----

delegate:
an object that can make decisions for other objects.

Of specific note is that the NSBrowser class treats its delegate as a data source.  This is Not the way things are done in Cocoa, so why does the Browser do it that way?
Because the Browser comes from a time when things were done that way, and hasn't yet been updated.  I personally expect an update or a replacement to the browser class in tiger.  This is based on the new "core data" marketing stuff I've read.  Core Data sounds like it is a default implementation of a generic data source, and for it to be useful, it would necessarily have to be compatible with interface elements.  Thus NSBrowser would have to support data sources.

