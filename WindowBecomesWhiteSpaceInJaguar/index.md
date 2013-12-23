---
layout: page
title: WindowBecomesWhiteSpaceInJaguar
---



I've got this strange problem that's happening in Jaguar, and not in Tiger. I have a modal window running above a sheet, and when I close the modal, I want to end and close the sheet too. This works fine on Tiger, but on Jaguar after the sheet closes, a big white rectangle shows up on the screen where the modal window was. Here's the code for closing the modal and sheet. Anyone have a clue why it's happening? It only happens after the sheet closes.

The code is called from the window controller class which controls both the sheet and modal window (both windows are stored in one nib). Maybe that's the problem? Maybe the modal window should be in its own window controller subclass and its own nib file??
    
...

General/[NSApp stopModal];
[modalWindow close];

General/[NSApp endSheet:[self window]];
General/self window] close];


----

hmm well I fixed this... not sure why it happens, but I just delayed by 0.25 seconds the closing of the sheet and it works fine :-)

*You might even be able to get away with a delay of zero using     performSelector:withObject:afterDelay: on     self. --[[JediKnil*
