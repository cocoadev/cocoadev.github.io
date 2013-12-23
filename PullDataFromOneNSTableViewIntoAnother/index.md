---
layout: page
title: PullDataFromOneNSTableViewIntoAnother
---



I am trying to get my feet wet with Cocoa, and I've managed to work my way through setting up a simple golf handicap calculator using Bindings.  A lot of the basic functionality is working, but I got stuck on sharing some of the data between my two classes / NSTableViews.  

The main window has an NSTableView listing the rounds of golf played.   I also have a panel where you can define the relevant information about the golf courses you play.  I am using NSArrayController for both of these.  I can select the golf course name via an NSPopup in the main NSTableView, and it gets that from the Courses table.   But, I am then stuck on how to pull over the other data ( (float)courseRating & (int)slopeRating ), which is in the same row in the Courses NSTableView and populate the information in the Rounds NSTableView.  I was trying to set it in my setCourseName method, so all fields are populated when the user selects the course name form the list.

With Cocoa Bindings handling all the shuffling of data, I am not too sure how to get to the data on my own.  Also, I'm more of a 'C' guy,  maybe I'm just missing some basic objective C OO concept.

Any suggestions, or examples?


----

Your difficulty grasping this stems from your incorrect assumption that the data 'belongs' to the table. Review the Cocoa documentation, search in particular for "Model View Controller" and explanations / demonstrations thereof. Once you've read that, read the introductory documentation on Cocoa Bindings. What you're trying to do is a very basic example of both these concepts and if you read the documentation, the solution will become obvious to you. Not a sermon, just a well-worn suggestion. :-)

----

Also check out the ModelViewController page here.

----

Sorry, that was a poor description of my problem.  I realize the data doesn't belong to the view..   But, I'm having a hard time translating the simple examples into a situation with two MVC "entities".  Course and Round are separate, and each have their own Model/NSArrayController/NSTableView.     Each is working fine independently, but sharing between them is not flying.

My best guess at this point is that I need to create a custom controller, which has access to both Models, and can get the needed data and set it in the other Model.   Can anyone give me a little hint, if I'm going down a reasonable path?
 
----

Perhaps we are unclear on the exact structure of the program you're describing (I feel like I am). If you need the same data in two table views, have them get the data either from the same controller or from two different controllers with the same model. If you're trying to do a master-detail interface, bind one controller to the selection of the other.

