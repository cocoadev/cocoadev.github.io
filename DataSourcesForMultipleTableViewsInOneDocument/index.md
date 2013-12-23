---
layout: page
title: DataSourcesForMultipleTableViewsInOneDocument
---



Thanks in advance for any help on this question...

I'm familiar with using a document instance as a data source for an NSTableView in the document's nib file, but I'm not sure I see how to do it if I have multiple table views that each need their own data source.  The only way I know how to do it is setting the data source in Interface Builder and that would have both table views retrieving the same data which wouldn't work.

I would imagine I have to use the MyDocument instance to manage instances of multiple classes that serve as data sources, and then set them as the data sources of the NSTableViews programatically rather than in Interface builder.  I really dont know how to do that (still a newbie).

Currently I have a split view with an NSTableView on the left that needs to access an array of song groups, and a NSTableView on the right that needs to display a list of songs & attributes.

This is still at the "experament & play" stage so I can afford to make mistakes.  Any info that could point me in the right direction?

Thanks!

CliffPruitt

----

I think I'm making some headway on this... I see I can use the setDataSource method of NSTableView.  I'm not 100% sure how to go about using my documents class to manage the data sources though.  I can probibly work through it, but does anyone maybe have a good example they wouldn't mind sharing?  I'm kind of on that place where I kinda grasp what I'm trying to do but am having a hard time getting it to "click"

Thanks,
CliffPruitt

----

In all of NSTableView's data source methods, the table view itself is the first parameter to the method. So that means you can point all of your table views at the same data source, and the data source can figure out which table is asking for data by examining the first parameter. For example:

    
- (int)numberOfRowsInTableView:(NSTableView *)tableView {
   if(tableView == bigTableView)
      return [bigTableViewArray count];
   else if(tableView == smallTableView)
      return [smallTableViewArray count];
   ...
}

----

Ahhh...  you know, I was looking at that parameter and trying to figure out how to use it in that way.  I couldn't figure out what to compare the passed NSTableView object to.  I was still at a point where I was using Init & AwakeFromNib with NSLog() to try & work out how to initialize my model & hadn't even gotten to the point where I had any IBOutlet variables created.  I see now its the IBOutlet variable in my document class that I need to use.

Thanks, thats a world of help.  At least I learned a lot by trying it the hard way.

One other related question...  Is it better practice to have your data source be your document class or a separate class or is that entirely dependant on the application at hand?  Is there any rule of thumb?

EIther way... I'm done for the night.  :-)

*You don't want to put the datasource methods in any of your model classes, but other than that anything is fair game. Most of the time, putting them in your main controller class works just fine, but if managing tableviews is a complex part of your app, you certainly can make a tableview controller class just for that purpose. -DF*

----

Thanks, I'll keep that in mind.

