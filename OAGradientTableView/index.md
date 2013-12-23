---
layout: page
title: OAGradientTableView
---



A subclass of NSTableView that displays the ITunesStyleGradient.

----

First, subclass NSTableView in the Interface Builder of your project.  After subclassing, make sure you set the Custom Class of your Table View in the Info panel.  Now create the file for your custom class.  Save interface builder.  Go to XCode/Project Builder and select your table view subclass.  Also, add the OmniAppKit framework into your framework group in your project.  Next, make sure you do this:

    #import <Cocoa/Cocoa.h>
#import <OmniAppKit/OAGradientTableView.h>

@interface myCustomTableView : OAGradientTableView
{
}
@end 

To explain.. you are importing the OAGradientTableView.h file into your class.  Then you are actually subclassing it in your subclass.  So it will use the code from the framework.  So there you go.  You will have a beautiful Gradient selection a la iTunes, iPhoto... and other beautiful iApps.  Have fun!

- magicmanpepe

----

OR, drag the OAGradientTableView.h header into Interface Builder and set the Custom Class of the table view directly to OAGradientTableView

----

I've seen people talking about a gradient selection, but have no idea what it means. Perhaps someone could post an explanation here? Looking in iTunes, etc. I don't see any difference at all in the selection, compared to any other plain old tableview selection. What is this?
*Never mind. Like a jackass, I was looking at the 'big' tables, not the left side-bar. Purdy effect!*

----

Why doesn't it draw the selected text in white? or am i missing something?

----

Check the header file "OAGradientTableView.h" - you will see a comments about adding a function to control the returned text color - I mucked about and finally got it to work. This is how you get your white highlight text.

----

How exactly did you accomplish the white selected text? I've ben trying to do this.. thanks.

----

In interface builder, drag the NSTextFieldCell control onto a column of your OAGradientTableView.  Now the column header gets the little triangle in the upper right hand corner.  Click on that and assign it's custom class to be either one that you subclassed and overrode  -textColor.  Or you can simply use omni's OATextWithIconCell (or at least go there for an example (as their header suggests)).

