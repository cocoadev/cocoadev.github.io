---
layout: page
title: NSOutlineViewBindingsHelp
---



I need some help with NSOutlineView. Essentially, what I'm trying to accomplish is somewhat like iTunes' source list. I have a NSOutlineView on the left side of a window which will hold 2 different levels of objects. On the right side, I have a tabless NSTabView with 3 views. The first is supposed to be visible when nothing in the NSOutlineView is selected. The second is supposed to be visible when any first-level object in the NSOutlineView is selected. It contains a NSTableView (which is handled by Cocoa bindings). The third is supposed to be visible when any second-level object is selected. It also contains a NSTableView, handled by Cocoa bindings. There are 3 different NSArrayControllers in my nib file - one containing top-level objects, one containing 2nd-level objects, and a third to handle objects that are not represented in the NSOutlineView but are listed in the table on the 3rd tab.

So, my question is, how do I get the NSOutlineView to display properly? I know that it doesn't have Cocoa bindings support, so how do I accomplish this? Do I set the controller to be the NSOutlineView's dataSource and/or its delegate? Basically, what needs to happen is that when a new node is created, if a top-level object is created the tab view needs to be changed to the appropriate tab and the new top-level object needs to be added to the NSOutlineView. Same for if a child object is created.

If it's still not clear what I'm trying to do, imagine if Mail.app had a different view when you selected "In" or "Out", for instance, instead of just listing all the emails in that folder and its subfolders - say it just had a table with a list of all the subfolders. Then, when you selected one of the subfolders (from the drawer), the list of emails would be displayed like it does now.

I could implement this with 3 NSTableViews, but I think it'd look a lot better if I combined the first 2 tables in a NSOutlineView.

Thanks!

----

You need to create (or reuse) a custom object to act as the delegate and/or datasource to the outline view.  This would be an object living in the controller layer of MVC, but not an NSObjectController or anything like that (necessarily).  Banish the bindings stuff from your head for this part, it isn't relevant. Just implement the delegate methods of NSOutlineView (see NSOutlineView docs) and datasource methods from the NSOutlineViewDataSource protocol that are appropriate  (e.g. in     outlineViewSelectionDidChange: you'll programmatically tell the tab view to change tabs).  When your custom controller object detects that the model has changed, you'll need to tell the outline view to refresh using     -[NSOutlineView reloadData].


Incidentally, you might want to consider changing the binding of the table view instead of making different table views on different tabs.

----

OK, I wanted to figure out how to do everything WITHOUT bindings - both for compatibility with pre-Panther systems and as a good exercise. After 6 hour of coding, it works! Instead of using the original app that I posted about above, I grabbed MailDemo from http://www.cocoadevcentral.com/articles/000080.php and OutlineMe from http://www.bignerdranch.com/source/outline.tgz. Basically, MailDemo has 2 classes: Mailbox and Email. Mailbox has an array of emails and a NSDictionary of properties. Email has an NSDictionary of properties. Well, it finally dawned on me how NSOutlineView nodes work: they have an array of children and a dictionary of properties! So it was SIMPLE to convert these classes into nodes - all I had to do was modify the source in my controller that was creating a Node (a custom class in OutlineMe) and instead create a Mailbox or Email as appropriate! This would even work with bindings - all one would have to do is strip the glue code and leave the delegate and dataSource methods. If anyone wants the source code, let me know.

Here's a snapshot:

http://homepage.mac.com/tylers/.Public/MailDemoOutlined.jpg

--TylerStromberg

----

I would love to see that code... magicmanpepe@gmail.com   Thanks!

----

I would also like to see the source for that.  I'm also currently struggling with setting up an NSOutlineView whose nodes (and their subsequent children) are model object instance variables of another model object; a model object that is bound to an NSTableView with bindings.  When a row is selected in the NSTableView, the corresponding model objects are displayed in the outline view.  Any help you could provide would be great.  java_nutt@charter.net.

James


----
Nice!! I would love to see the code as well jwilliams@blansys.com i have been fighting with bindings and outline view for 2 weeks now on this same subject.

----
May I have a look at the code. I am looking to implement similar thing to display articles and its content.

Thanks in advance
aqueeL
aqueel_saifi@yahoo.co.in

----
OK, I recreated the source and put it up on my site (complete with the XCode project): http://homepage.mac.com/tylers/Development/code/MailDemo/

http://homepage.mac.com/tylers/Development/images/NewMailDemoOutlined.jpg

TylerStromberg
----

