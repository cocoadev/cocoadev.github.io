---
layout: page
title: HowToPrintMultipleImagesWithoutALargeNSView
---


I have an NSView with an NSImage as background and some text that will display someones address from a database. 

Lets say the user wants to print ONE of these off, well that would be easy i presume i just print the NSView.

But if the user wants to print out 50....

a) I make another huge NSView, copy the contents of the original NSView (each with the text slightly modified), then print the whole ugly pointless (and probably slow) thing out.

b) I make some code (psudo) such as...

//-----------------------

int count =1;

for(i=0;i<50;i++){

myNSView->myNSTextView = getAdresssTXTFromDataBase(); //this i can do, the following is where i need help...

[printbuffer  addView:myNSView];

if(count == 4){ [printbuffer addPabeBreak]; count=1;} //i can fit only four of these images on a page, therefore i need a "pageBreak"?

count++;

}

[printbuffer print];


//-----------------------

this way the user does not have to see every image(as he has no reason to) and its all just a little bit neater.

Could you point me in the direction of the technolgies i need to research to get this scenario to work. thankyou very much

----
This might be a little hackish, but.... What if you got PDF data from each view, then inserted those PDFs into an NSTextView using NSTextAttachment, then printed the whole thing? NSTextView should take care of pag ination and all the rest of that stuff for you.

My post was rejected as spam because it contained the word "pag ina" but without the space. How lame.

----
Thats an interesting and relativly simple way of looking at it. Thanks i will look into it. (sorry my first time here, i dont really know how this site works and this is the only way i know how to reply)

----

... and now for a second opinion. :-) You're guilty of PrematureOptimization. Your "PersonView" class knows how to draw itself. Let it. Creating a simple, generic view that can 'tile' subviews is trivial (read "done all the time"). Why not try it and see if it really is that slow? I'm guessing it'd be no slower than the approach suggested above. In fact, it may be a bit faster since you're not first drawing each view, then arranging the drawings with all the overhead of the text system. That seems a bit silly to me.

----

I think the  first persons opinion was appealing because it seems technologicaly easier. I have been trying to do paging with NSView , and i cant work out how to stop my images from being 'clipped'. Though maybe i should have another try....

----

You **really** need to read the documentation. This is one of the most basic parts of learning the Cocoa printing system.

file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/Printing/index.html

Give it a read.

