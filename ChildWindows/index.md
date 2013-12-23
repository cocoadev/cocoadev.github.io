---
layout: page
title: ChildWindows
---

I'm an experienced Windows and Web developer but new to Cocoa. After creating a bunch of test projects, I decided to try to create a simple RSS reader application as a learning project. During this process, Ive become unsure how to handle a pop-up window and wanted to get others advice. Here's the details:

I've created a Cocoa Application (not-document based). I've defined a "Feed" class with accessor methods for it's properties. Inside my MainMenu nib file I've got my main window with an NSTableView and an Add button. I've also defined an AppController object which has the outlets and actions required to handle the main window's functionality. The idea is that the NSTableView would list all feeds that the user has subscribed to. The Add button would pop up another window and ask them for the URL to the RSS feed and for a name for the feed. When they enter this, it would be added to the table. 

I do have this working but I'm unsure whether I've done it the correct way (or at least following the right conventions). I've created another nib file called AddFeed. Inside of it, I've got an NSPanel with the controls layed out on it. I've also defined an AddFeedController object with the outlets and actions required to make that panel work. The part I'm trying to figure out is the mechanism to display this "Add Feed" window from the main window. I'm seeing at least 3 options:

1) Make it an independant window. This would be like the "Downloads" window in Safari.
2) Make it an independant modal window. This would be like the "Open File" window in Safari.
3) Make it a modal child window. This would be like the Add Bookmark window in Safari.

So my question is two-fold:
1) What would be the general convention here?
2) What code would I use to achieve whichever option you're suggesting? (i.e. just the actual pop-up part of the code)

Thanks for any thoughts you can contribute.

----

How 'bout none of the above? Instead of using a dialog or window add a dummy entry to the feed table. Have the table show the feed name and location (at least 2 columns). When the user presses the Add button you'd create a new entry ("Untitled Feed") in the table and start the editing proccess so they don't have to find the new row and begin editing it themselves. Once the name and location for the new feed is entered check its validity and go.

