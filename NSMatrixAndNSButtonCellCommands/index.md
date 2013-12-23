---
layout: page
title: NSMatrixAndNSButtonCellCommands
---

Hey it's me again, hehe. I hope I'm not bothering but I really want to lear so someday I can teach someone too ;-)

I've got a NSMatrix with 4 radio buttons as NSButtonCells. I need to know which one is the selected one before continuing my action, but I don't know how to check them, so I tried to do it by using their titles (the NSMatrix is called myChoice):


- (IBAction)search:(id)sender
{
  if (myChoice selectedCell] title] == @"Artist")
  {
      // do something here
  }
  else
  {
     [[NSLog (@"Error!");
  }
}

But everytime I try this (with any of the four radio buttons) NSLog returns "Error", which means my code's not working. Ive also tried replacing @"Artist" by an NSString with that value, but won't work too.

What should I do?
Thanx in advance!

----

You should be using the NSString method -isEqualToString:, not the == operator, to compare strings (i.e. [myChoice selectedCell] title] isEqualToString:@"Artist"]).  The == operator will only return true if the strings are the same object, while the -isEqualToString: method compares the strings character-by-character to see if they're equivalent.  -- Bo

----

Wow, I would never think about using isEqualToString. Thank you so much Bo.

----

Remember, [[NSButton inherits from NSControl, and NSControl has an *int* member, *tag*.  You can set that in InterfaceBuilder.  Then, switch on *[sender tag]*.

----

I've got each radio button having its own tag, but in my IBAction,      [sender tag]  return the matrix's tag ( I set it to an odd number, just to be certain ). Now, the matrix isn't connected to the IBOutlet, rather I double-clicked the cells and connected them all to the same outlet.

So, my question is, WTF is going on????

I got around it by querying      [[thematrix selectedCell] tag] , but it's still a peculiarity.

