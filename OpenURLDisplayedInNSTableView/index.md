---
layout: page
title: OpenURLDisplayedInNSTableView
---

I want to make a simple app that's got two text fields and one tableview. So you type a name in one field (called nameField) and the URL on the URLField. Then you press a button and both informations go through an array to the table view. Everything is done, even the dataSource and button actions.

So, I would like to know how do I get to make the URLs in the tableView be opened by a browser, when you click on it. The bad part is that I have no clue at all of how to do it.

 

----

A real simple method...use an action method from the table, verify its a url in the action and then launch a browser using NSWorkspace. If you want to add more complexity you will have to use an NSCell from memory.

Something like this:

    
- (void)awakeFromNib { // or another method
// The 'myTableView' could be an outlet or otherwise-previously-declared NSTableView
[myTableView setTarget:self];
[myTableView setDoubleAction:@selector(myOtherMethod)];
}

- (void)myOtherMethod
{

NSURL * myURL = ... // Ask the table for its selected row, ask your datasource for the URL string at that row, try to make an NSURL

if (myURL) // Check it for validity! if it's nil, the string was incorrect.
     [[NSWorkspace sharedWorkspace] openURL:myURL];
else
    NSBeep(); // Or some more user-friendly way of saying, "BZZZT! INVALID URL! TRY AGAIN!
}


I think this should work, though I've never done it myself. And if this looks incomplete or sketchy, its because its 3AM. Tell me how it goes, good night.

P.S. Add yourself to the people list.

- JohnDevor

*Code example above corrected for clarity.*

----

Just to expand on my original idea with code. :-)

First step is to create an action in your desired class in interface builder. Second step is to link the table to that action. Now you will have something like this:

    

- (IBAction)tableClick:(id)sender
{
    // Now we get the table row, use your global array or dictionary to get the URL data and send it to NSWorkSpace.
    [[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:[[NSArray objectAtIndex:[sender selectedRow]] objectForKey:@"URL"]]];
}


